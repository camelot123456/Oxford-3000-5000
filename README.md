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

- Sau khi tạo ra sheet mới chứa 20 từ vựng, nhiệm tiếp theo là tìm nghĩa của từ ở cột bên cạnh và cách phiên âm
- Chuyển sang định dạng csv và dán vào ChatGPT và yêu cầu chuyển định dạng csv sang định dạng của `RemNote` để có thể học từ vựng theo phương pháp `Spaced Repetition Systems`
---
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
---
### **Tập đọc:**
- Sử dụng [Chat GPT](https://chatgpt.com/) để tạo đoạn hội thoại + [TTSReader](https://ttsreader.com/player/)
- Prompt cho ChatGPT:

        Xin chào, tôi sẽ cung cấp danh sách 20 từ vựng tiếng anh ở định dạng .CSV, bạn hãy tạo giúp tôi 1 đoạn hội thoại để tôi luyện tập giao tiếp và phải bao gồm các yêu cầu sau:
        - level: a1
        - chủ đề: công việc, văn phòng, đi làm
        - yêu cầu ngữ pháp: hiện tại đơn, hiện tại tiếp diễn
        - tiêu chí: natural, emotion
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

---
### **Luyện nghe:**
Copy đoạn hội thoại do [Chat GPT](https://chatgpt.com/) tạo ra và dán vào [TTSReader V3.6.0 - TTSReader's Text to Speech Player](https://ttsreader.com/player/)

---

### **Luyện nói:**
Sử dụng [ChatGPT Voice]() để luyện giao tiếp

---

## **2. Các bước thực hành:**
1. Chuyển đổi định dạng file `Oxford-5000.csv` thành `Oxford-5000.xlsx`
2. Nhân bản sheet gốc để dự phòng
3. Dán đoạn mã App Script vào `.xlsx` và nhấn nút Run để chọn ngẫu nhiên 20 từ vựng
4. Bổ sung thêm cột nghĩa và phiên âm (mặc định giọng Mỹ)
5. Dán prompt vào [ChatGPT] để tự động tạo đoạn hội thoại
6. Dán đoạn hội thoại vào [TTSReader] để tạo đoạn ghi âm hội thoại
7. Luyện đọc, luyện nghe đoạn hội thoại
8. Cuối ngày, học thuộc từ vựng bằng phương pháp Spaced Repetition Systems ([RemNote](https://www.remnote.com/), [Quizlet](https://quizlet.com/), [Anki](https://ankiweb.net/about))
9. Làm bài test về ngữ pháp và từ vựng bằng ứng dụng [Rem Note](https://www.remnote.com/)
