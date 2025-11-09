# TTS Dialogue Generator
Script tts_dialogue_with_header.py giúp bạn tạo file audio (.mp3) từ đoạn hội thoại text với nhiều giọng nói khác nhau, tốc độ nói tùy chỉnh và khoảng nghỉ giữa các câu. Hỗ trợ Edge-TTS với giọng tự nhiên của Microsoft.
## 1. Yêu cầu
- Python ≥ 3.10
- FFmpeg (phải cài đặt và có trong PATH) 
Thư viện Python:
```bash
pip install edge-tts pydub
```
FFmpeg `ffmpeg-release-essentials.zip` trên Windows có thể tải từ: https://ffmpeg.org/download.html
Và thêm folder bin vào PATH.
## 2. Cấu trúc file hội thoại `dialogue.txt`
File `.txt` gồm header (tham số) và nội dung hội thoại.
### 2.1 Header
Header bắt đầu bằng `#` và nằm ở đầu file. Ví dụ:
```bash
# Olivia=en-US-AriaNeural
# Mark=en-US-GuyNeural
# rate=-5%
# pause=800
```
- TênNhânVật=Giọng: Gán giọng cho từng nhân vật
   `Ví dụ: Olivia=en-US-AriaNeural`
- rate: Tốc độ nói, ví dụ `-10%` (chậm hơn), `+5%` (nhanh hơn)
- pause: Thời gian nghỉ giữa các câu (ms), ví dụ `800` = 0.8 giây

### 2.2 Nội dung hội thoại
Mỗi dòng có định dạng:
```txt
TênNhânVật: Nội dung câu nói
```
Ví dụ:
```txt
voice1: Hi Mark! How are you today?
voice2: I'm doing well, thanks!
```
>⚠️ <span style="color:red; font-weight:bold;">Lưu ý:</span> Tên nhân vật trong header và trong hội thoại phải trùng nhau.
## 3. Cấu trúc thư mục
```bash
project/
│
├─ dialogue.txt              # File hội thoại và header
├─ tts_dialogue_with_header.py  # Script Python
├─ tmp/                      # Thư mục tạm lưu các file mp3 tách nhỏ
└─ dialogue.mp3              # File kết quả sau khi chạy script
```
Code `tts_dialogue_with_header.py`:
```python
import asyncio
import edge_tts
import os
from pydub import AudioSegment

# Hàm chính
async def main():
    speaker_voices = {}
    rate = "0%"
    pause_ms = 800
    bitrate = "14k"  # 🔹 Bitrate nén MP3
    channels = 1  # 🔹 Mono

    # Đọc file hội thoại
    with open("dialogue.txt", "r", encoding="utf-8") as f:
        lines = [line.strip() for line in f if line.strip()]

    dialogue_lines = []
    for line in lines:
        if line.startswith("#"):
            key_value = line[1:].split("=", 1)
            if len(key_value) == 2:
                key, value = key_value
                key = key.strip()
                value = value.strip()
                if key.lower() == "rate":
                    rate = value
                elif key.lower() == "pause":
                    pause_ms = int(value)
                elif key.lower() == "bitrate":
                    bitrate = value
                else:
                    # key = tên nhân vật, value = giọng
                    speaker_voices[key] = value
        else:
            dialogue_lines.append(line)

    if not speaker_voices:
        speaker_voices = {"Olivia": "en-US-AriaNeural", "Mark": "en-US-GuyNeural"}

    os.makedirs("tmp", exist_ok=True)
    audio_files = []

    print("🎧 Voice configuration:")
    for k, v in speaker_voices.items():
        print(f"   {k} = {v}")
    print(f"   Rate = {rate}")
    print(f"   Pause = {pause_ms} ms")
    print(f"   Bitrate = {bitrate}\n")

    # Tạo audio cho từng câu
    for i, line in enumerate(dialogue_lines):
        if ":" not in line:
            continue
        speaker, text = line.split(":", 1)
        speaker = speaker.strip()
        text = text.strip()

        voice = speaker_voices.get(speaker)
        if not voice:
            voice = "en-US-AriaNeural"  # fallback
            print(f"⚠️ No voice assigned for {speaker}, using default {voice}")

        out_file = f"tmp/part_{i:02d}.mp3"
        print(f"🎙️ {speaker} → {voice} → '{text}'")

        communicate = edge_tts.Communicate(text, voice, rate=rate)
        await communicate.save(out_file)
        audio_files.append(out_file)

    # Ghép các file MP3 tạm
    combined = AudioSegment.empty()
    for file in audio_files:
        segment = AudioSegment.from_mp3(file)
        combined += segment + AudioSegment.silent(duration=pause_ms)

    # 🔹 Xuất file MP3 cuối cùng với nén
    output_file = "dialogue.mp3"
    combined.export(
        output_file, format="mp3", bitrate=bitrate, parameters=["-ac", str(channels)]
    )
    print(f"\n✅ Done! Saved as {output_file}")
    print(
        f"📉 File đã được nén: Mono, {bitrate} bitrate, pause {pause_ms}ms giữa các câu"
    )

# Chạy
asyncio.run(main())
```
Thư mục `tmp/` sẽ được tạo tự động nếu chưa tồn tại.
## 4. Cách sử dụng
1. Chuẩn bị file `dialogue.txt`
    ```plain
    # voice1=en-US-AriaNeural
    # voice2=en-US-GuyNeural
    # rate=-5%
    # pause=800
    # bitrate=14k

    voice1: Hi Mark! How are you today?
    voice2: I'm doing well, thanks!
    voice1: I'm good, thanks for asking!
    voice2: Nice talking to you too, Aria! Have a great day!
    ```
2. Chạy script
    ```bash
    python tts_dialogue_with_header.py
    ```
3. Kết quả
    - File dialogue.mp3 chứa toàn bộ đoạn hội thoại với:
        - Giọng voice1 = AriaNeural
        - Giọng voice2 = GuyNeural
        - Tốc độ nói = -5%
        - Nghỉ giữa câu = 0.8 giây
        - Nén .mp3 = 14k
    - Các file tạm được lưu trong tmp/ (có thể xóa sau khi ghép xong).
## 5. Tuỳ chọn nâng cao
- Thêm nhiều nhân vật bằng header:
    ```yaml
    # voice1=en-US-AriaNeural
    # voice2=en-US-GuyNeural
    # voice3=en-GB-LibbyNeural
    ```
- Thay đổi tốc độ nói từng nhân vật bằng cách thêm rate riêng hoặc sửa chung:
    ```txt
    # rate=-10%
    ```
- Thay đổi thời gian nghỉ giữa câu:
    ```less
    # pause=1000  # 1 giây
    ```
- Thay đổi Bitrate nén MP3 (giảm kích thước file dialogue.mp3):
    ```less
    # bitrate=14k
    ```
## 6. Lưu ý
- Tên nhân vật phải trùng giữa header và nội dung.
- Edge-TTS yêu cầu kết nối internet để sinh giọng.
- Nếu giọng không tồn tại hoặc tên nhân vật chưa gán giọng → script sẽ dùng giọng mặc định: `en-US-AriaNeural`.