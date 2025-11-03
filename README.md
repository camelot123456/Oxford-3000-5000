# **Kế hoạch tự học tiếng anh giao tiếp tại nhà**

## **1. Soạn tài liệu mỗi ngày:**

### **Từ vựng:**

- Sử dụng danh sách 5000 từ vựng Oxford, sau đó phân loại level, rồi chọn ngẫu nhiên 20 từ không trùng lặp
- Sử dụng App Script:
```javascript
function selectRandomWords() {
  const inputSheetName = 'OxfordWords';
  const outputSheetName = 'SelectedWords';
  const randomNumberWord = 20;
  const selectLevel = 'a1';

  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const inputSheet = ss.getSheetByName(inputSheetName);
  const outputSheet = ss.getSheetByName(outputSheetName) || ss.insertSheet(outputSheetName);

  const data = inputSheet.getDataRange().getValues();
  const headers = data[0];
  const rows = data.slice(1);

  const wordIndex = headers.indexOf('word');
  const classIndex = headers.indexOf('class');
  const levelIndex = headers.indexOf('level');
  const selectedIndex = headers.indexOf('selected');

  // if (selectedIndex === -1) {
  //   inputSheet.getRange(1, headers.length + 1).setValue('selected');
  // }

  const eligibleRows = rows
    .map((row, i) => ({ row, i }))
    .filter(({ row }) => row[levelIndex] === selectLevel && row[selectedIndex] !== 1);

  if (eligibleRows.length < randomNumberWord) {
    throw new Error(`Không đủ từ cấp độ ${selectLevel} chưa chọn (${eligibleRows.length} < ${randomNumberWord})`);
  }

  const selected = getRandomSample(eligibleRows, randomNumberWord);

  const outputData = selected.map(({ row }) => [
    row[wordIndex],
    row[classIndex],
    row[levelIndex],
  ]);

  // Ghi dữ liệu ra sheet output
  outputSheet.clearContents();
  outputSheet.getRange(1, 1, 1, 3).setValues([['word', 'class', 'level']]);
  outputSheet.getRange(2, 1, outputData.length, 3).setValues(outputData);

  // Đánh dấu đã chọn = 1 trong sheet gốc
  selected.forEach(({ i }) => {
    inputSheet.getRange(i + 2, selectedIndex + 1).setValue(1);
  });
}

function getRandomSample(array, n) {
  const result = [];
  const usedIndices = new Set();
  while (result.length < n) {
    const i = Math.floor(Math.random() * array.length);
    if (!usedIndices.has(i)) {
      usedIndices.add(i);
      result.push(array[i]);
    }
  }
  return result;
}
```

```javascript
function selectRandomWords() {
  const inputSheetName = 'OxfordWords';
  const outputSheetName = 'SelectedWords';
  const randomNumberWord = 20;

  // 🧩 Cấu hình tỉ lệ chọn từ theo level
  const lowLevel80Percent = 'a1';
  const highLevel20Percent = 'a2';
  const ratioLow = 0.8; // 80% A1
  const ratioHigh = 0.2; // 20% A2

  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const inputSheet = ss.getSheetByName(inputSheetName);
  const outputSheet = ss.getSheetByName(outputSheetName) || ss.insertSheet(outputSheetName);

  const data = inputSheet.getDataRange().getValues();
  const headers = data[0];
  const rows = data.slice(1);

  const wordIndex = headers.indexOf('word');
  const classIndex = headers.indexOf('class');
  const levelIndex = headers.indexOf('level');
  const selectedIndex = headers.indexOf('selected');

  const numLow = Math.round(randomNumberWord * ratioLow);
  const numHigh = randomNumberWord - numLow;

  // 🔹 Lọc các hàng đủ điều kiện cho mỗi cấp độ
  const eligibleLow = rows
    .map((row, i) => ({ row, i }))
    .filter(({ row }) => row[levelIndex] === lowLevel80Percent && row[selectedIndex] !== 1);

  const eligibleHigh = rows
    .map((row, i) => ({ row, i }))
    .filter(({ row }) => row[levelIndex] === highLevel20Percent && row[selectedIndex] !== 1);

  // 🔸 Kiểm tra số lượng đủ chưa
  if (eligibleLow.length < numLow) {
    throw new Error(`Không đủ từ cấp độ ${lowLevel80Percent} chưa chọn (${eligibleLow.length} < ${numLow})`);
  }
  if (eligibleHigh.length < numHigh) {
    throw new Error(`Không đủ từ cấp độ ${highLevel20Percent} chưa chọn (${eligibleHigh.length} < ${numHigh})`);
  }

  // 🔹 Chọn ngẫu nhiên theo từng nhóm
  const selectedLow = getRandomSample(eligibleLow, numLow);
  const selectedHigh = getRandomSample(eligibleHigh, numHigh);

  const selected = [...selectedLow, ...selectedHigh];

  const outputData = selected.map(({ row }) => [
    row[wordIndex],
    row[classIndex],
    row[levelIndex],
  ]);

  // 🧾 Ghi dữ liệu ra sheet output
  outputSheet.clearContents();
  outputSheet.getRange(1, 1, 1, 3).setValues([['word', 'class', 'level']]);
  outputSheet.getRange(2, 1, outputData.length, 3).setValues(outputData);

  // ✅ Đánh dấu đã chọn = 1 trong sheet gốc
  selected.forEach(({ i }) => {
    inputSheet.getRange(i + 2, selectedIndex + 1).setValue(1);
  });
}

function getRandomSample(array, n) {
  const result = [];
  const usedIndices = new Set();
  while (result.length < n) {
    const i = Math.floor(Math.random() * array.length);
    if (!usedIndices.has(i)) {
      usedIndices.add(i);
      result.push(array[i]);
    }
  }
  return result;
}
```

- Sau khi tạo ra sheet mới chứa 20 từ vựng, nhiệm tiếp theo là tìm nghĩa của từ ở cột bên cạnh và cách phiên âm
- Chuyển sang định dạng csv và dán vào ChatGPT và yêu cầu chuyển định dạng csv sang định dạng của `RemNote` để có thể học từ vựng theo phương pháp `Spaced Repetition Systems`

### **Ngữ pháp:**

- Từ loại (Parts of Speech) – nền tảng quan trọng
- Các Thì Cơ Bản & Thường Dùng:
    - Hiện tại đơn
    - Hiện tại tiếp diễn
    - Quá khứ đơn
    - Quá khứ tiếp diễn
    - Tương lai gần
    - Tương lai đơn
    - Hiện tại hoàn thành
- Câu hỏi thường gặp trong giao tiếp
- Câu điều kiện (Conditional Sentences)
    - Loại 0: Chân lý (If you heat ice, it melts.)
    - Loại 1: Có thể xảy ra (If it rains, I will stay home.)
    - Loại 2: Không thực ở hiện tại (If I were rich, I would travel.)
    - Loại 3: Không thực quá khứ (If I had studied, I would have passed.)
- Câu mệnh lệnh (Imperatives)
- Câu bị động (Passive Voice)
    - So sánh (Comparison)
    - So sánh hơn (comparative): taller, more beautiful
    - So sánh nhất (superlative): the tallest, the most beautiful
    - So sánh bằng (as...as): He is as tall as me.
- Modals (Động từ khuyết thiếu)
- Liên kết câu (Linking devices)
- Câu gián tiếp (Reported Speech) (giao tiếp nâng cao hơn một chút)
- Mạo từ (Articles): a, an, the
- Sở hữu (Possessives)
- Các cấu trúc câu quan trọng

### **Chủ đề để luyện giao tiếp:**
🔝 TOP 35 Chủ đề Giao tiếp Thiết yếu – Sắp xếp theo mức độ phổ biến & cần thiết:

| STT | Chủ đề                           | Mức độ sử dụng | Lý do thiết yếu                         |
| --- | -------------------------------- | -------------- | --------------------------------------- |
| 1   | Giới thiệu bản thân              | 🌟🌟🌟🌟🌟     | Mở đầu mọi tình huống                   |
| 2   | Chào hỏi                         | 🌟🌟🌟🌟🌟     | Tương tác đầu tiên                      |
| 3   | Tạm biệt                         | 🌟🌟🌟🌟🌟     | Đóng cuộc trò chuyện                    |
| 4   | Hỏi thăm sức khỏe                | 🌟🌟🌟🌟🌟     | Tạo quan hệ thân thiện                  |
| 5   | Gọi món ăn                       | 🌟🌟🌟🌟🌟     | Rất phổ biến khi đi ăn                  |
| 6   | Mua sắm                          | 🌟🌟🌟🌟🌟     | Rất thực tế trong đời sống              |
| 7   | Hỏi đường                        | 🌟🌟🌟🌟🌟     | Quan trọng khi đi lại                   |
| 8   | Nói về thời gian                 | 🌟🌟🌟🌟🌟     | Dùng hàng ngày                          |
| 9   | Mô tả người                      | 🌟🌟🌟🌟🌟     | Hay dùng trong mô tả bạn bè, người thân |
| 10  | Gia đình                         | 🌟🌟🌟🌟🌟     | Chủ đề phổ biến                         |
| 11  | Bạn bè                           | 🌟🌟🌟🌟       | Giao tiếp xã hội                        |
| 12  | Hoạt động hàng ngày              | 🌟🌟🌟🌟       | Gắn liền với thực tế                    |
| 13  | Thời tiết                        | 🌟🌟🌟🌟       | Dễ mở đầu câu chuyện                    |
| 14  | Hỏi – đưa lời khuyên             | 🌟🌟🌟🌟       | Dùng để trao đổi quan điểm              |
| 15  | Nói về sở thích                  | 🌟🌟🌟🌟       | Giao tiếp tự nhiên                      |
| 16  | Giao tiếp qua điện thoại         | 🌟🌟🌟🌟       | Cần thiết trong công việc               |
| 17  | Giao tiếp công sở                | 🌟🌟🌟🌟       | Làm việc hiệu quả                       |
| 18  | Email – đặt lịch hẹn             | 🌟🌟🌟🌟       | Rất thực tế trong công việc             |
| 19  | Phỏng vấn xin việc               | 🌟🌟🌟🌟       | Giao tiếp nghề nghiệp                   |
| 20  | Giao tiếp tại khách sạn          | 🌟🌟🌟🌟       | Phổ biến khi đi du lịch                 |
| 21  | Sân bay và nhập cảnh             | 🌟🌟🌟🌟       | Quan trọng khi ra nước ngoài            |
| 22  | Đặt lịch hẹn                     | 🌟🌟🌟🌟       | Cần trong công việc và đời sống         |
| 23  | Từ chối và xin lỗi lịch sự       | 🌟🌟🌟🌟       | Rèn kỹ năng mềm                         |
| 24  | Giao tiếp khi mua vé/đi lại      | 🌟🌟🌟🌟       | Du lịch, công tác                       |
| 25  | Giao tiếp khi đi bệnh viện       | 🌟🌟🌟🌟       | Tình huống khẩn cấp                     |
| 26  | Giao tiếp ngân hàng – tài chính  | 🌟🌟🌟         | Quản lý tiền bạc                        |
| 27  | Giao tiếp trong nhóm             | 🌟🌟🌟         | Làm việc nhóm hiệu quả                  |
| 28  | Giao tiếp qua email công việc    | 🌟🌟🌟         | Kỹ năng văn phòng cơ bản                |
| 29  | Đưa quan điểm – tranh luận       | 🌟🌟🌟         | Giao tiếp nâng cao                      |
| 30  | Giao tiếp trong tiệc – party     | 🌟🌟🌟         | Giao tiếp xã hội                        |
| 31  | Tình yêu – quan hệ               | 🌟🌟🌟         | Giao tiếp cá nhân                       |
| 32  | Công nghệ – mạng xã hội          | 🌟🌟🌟         | Gắn liền đời sống hiện đại              |
| 33  | Môi trường – thời sự             | 🌟🌟🌟         | Thiết yếu khi thảo luận xã hội          |
| 34  | Đời sống hôn nhân – nuôi dạy con | 🌟🌟           | Thực tế với người đã lập gia đình       |
| 35  | Văn hóa – phong tục              | 🌟🌟           | Quan trọng khi giao lưu quốc tế         |


### **Tập đọc với đoạn hội thoại:**
- Sử dụng [Chat GPT](https://chatgpt.com/) để tạo đoạn hội thoại + [TTSReader](https://ttsreader.com/player/)
- Prompt cho ChatGPT:

        Xin chào, tôi sẽ cung cấp danh sách 20 từ vựng tiếng anh ở định dạng .CSV, bạn hãy tạo giúp tôi 1 đoạn hội thoại để tôi luyện tập giao tiếp và phải bao gồm các yêu cầu sau:
        - level: a1
        - chủ đề: công việc, văn phòng, đi làm
        - yêu cầu ngữ pháp: hiện tại đơn, hiện tại tiếp diễn
        - tiêu chí: tính tự nhiên, cảm xúc, sử dụng các câu tập phản xạ
        - tích hợp công cụ tạo file giọng nói: https://ttsreader.com/
        - định dạng đoạn hội thoại như sau:
        {{set: lang=en; name=Aria; }}
        ghi câu hội thoại của Aria ở đây
        {{set: lang=en; name=Mark; }}
        ghi câu hội thoại của Mark ở đây
        - danh sách từ vựng:
        word,class,level
        telephone,verb,a1
        person,noun,a1
        ...
        bạn hãy bổ sung GHI CHÚ NGỮ PHÁP ở cuối đoạn văn để tôi có thể biết bạn đang sử sử dụng nhưng ngữ pháp nào trong đoạn hội thoại.

        Xin cảm ơn!


### **Luyện nghe:**
Copy đoạn hội thoại do [Chat GPT](https://chatgpt.com/) tạo ra và dán vào [TTSReader V3.6.0 - TTSReader's Text to Speech Player](https://ttsreader.com/player/)



### **Luyện nói:**
Sử dụng [ChatGPT Voice]() để luyện giao tiếp

---

## **2. Các bước thực hiện:**
1. Chuyển đổi định dạng file `Oxford-5000.csv` thành `Oxford-5000.xlsx`
1. Nhân bản sheet gốc để dự phòng
1. Dán đoạn mã App Script vào `.xlsx` và nhấn nút Run để chọn ngẫu nhiên 20 từ vựng
1. Bổ sung thêm cột nghĩa và phiên âm (mặc định giọng Mỹ)
1. Dán prompt vào [ChatGPT](https://chatgpt.com/) để tự động tạo đoạn hội thoại
1. Dán đoạn hội thoại vào [TTSReader](https://ttsreader.com/player/) để tạo đoạn ghi âm hội thoại
1. Luyện đọc, luyện nghe đoạn hội thoại
1. Cuối ngày, học thuộc từ vựng bằng phương pháp `Spaced Repetition Systems` ([RemNote](https://www.remnote.com/), [Quizlet](https://quizlet.com/), [Anki](https://ankiweb.net/about))
1. Làm bài test về ngữ pháp và từ vựng bằng ứng dụng [Rem Note](https://www.remnote.com/)

## **3. Yêu cầu:**
1. Xem nhanh 20 từ vựng mới, không học thuộc trực tiếp
1. Dịch nghĩa của từ
1. Tập phát âm chính xác mỗi từ
1. Tạo đoạn hội thoại, đọc lướt qua
1. Nghe đoạn hội thoại và đoán nghĩa
1. Tập trung các câu phản xạ
1. Đọc nhái theo các câu
1. Ghi chú ngữ pháp trong hội thoại
1. Bài tập kiểm tra số từ đã thuộc dùng `RemNote`
1. Cứ 3 buổi sẽ sử dụng `ChatGPT` để luyện nghe nói 1:1 trực tiếp với AI
---

#### *Chủ đề đầy để luyện giao tiếp:*

🔹 A. Giao tiếp cơ bản (Essential Daily Topics – ~25 chủ đề)
1. Giới thiệu bản thân
1. Chào hỏi
1. Tạm biệt
1. Hỏi thăm sức khỏe
1. Nói về thời gian
1. Hỏi đường
1. Gọi món tại nhà hàng
1. Mua sắm
1. Tính tiền – trả giá
1. Hỏi và mô tả địa điểm
1. Nói về thời tiết
1. Gia đình
1. Bạn bè
1. Mô tả người (ngoại hình, tính cách)
1. Hoạt động hằng ngày
1. Nói về nghề nghiệp
1. Mô tả nhà cửa
1. Hỏi ý kiến và đưa lời khuyên
1. Bày tỏ cảm xúc
1. Hỏi sở thích
1. Kế hoạch tương lai
1. Đặt lịch hẹn
1. Cách từ chối lịch sự
1. Xin lỗi và cảm ơn
1. Gọi điện thoại

🔹 B. Giao tiếp du lịch – sinh hoạt (Travel & Living – ~15 chủ đề)
1. Đặt phòng khách sạn
1. Sân bay và nhập cảnh
1. Mua vé (tàu, xe, máy bay)
1. Hỏi về tour du lịch
1. Tình huống khẩn cấp
1. Giao tiếp tại bệnh viện / hiệu thuốc
1. Giao tiếp tại ngân hàng
1. Giao tiếp tại bưu điện
1. Giao tiếp khi thuê xe
1. Mất đồ – báo cảnh sát
1. Giao tiếp tại trạm xăng
1. Giao tiếp tại quầy thông tin
1. Phỏng vấn ngắn (visa, nhập cư)
1. Đổi tiền – tỷ giá
1. Giao tiếp khi đặt hàng online

🔹 C. Giao tiếp công việc – học tập (Work & Study – ~15 chủ đề)
1. Giao tiếp trong văn phòng
1. Giao tiếp trong cuộc họp
1. Giao tiếp qua email
1. Đặt lịch – dời lịch
1. Phỏng vấn xin việc
1. Giao tiếp với sếp
1. Thuyết trình cơ bản
1. Giao tiếp nhóm – teamwork
1. Giao tiếp khi đào tạo – training
1. Giao tiếp trong trường học
1. Nói về mục tiêu nghề nghiệp
1. Giao tiếp khách hàng
1. Đàm phán, thương lượng
1. Kỹ năng viết CV
1. Giải quyết xung đột nơi làm việc

🔹 D. Giao tiếp mở rộng (Extra – ~10 chủ đề)
1. Chia sẻ quan điểm
1. Nói về văn hóa – phong tục
1. Thể thao
1. Âm nhạc – phim ảnh
1. Mạng xã hội – công nghệ
1. Môi trường – biến đổi khí hậu
1. Đời sống hôn nhân
1. Trẻ em – nuôi dạy con
1. Giao tiếp trong tiệc tùng
1. Giao tiếp trong tình yêu – mối quan hệ