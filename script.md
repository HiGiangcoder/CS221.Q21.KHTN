# CHAPTER 3.4 – SAMPLING SENTENCES FROM A LANGUAGE MODEL

---

## 1. Vì sao cần Sampling?

Một cách quan trọng để hiểu một language model “biết” điều gì là **lấy mẫu (sample)** từ nó.

Language model biểu diễn một **phân phối xác suất trên các câu**.

Sampling từ một phân phối nghĩa là:

> Chọn ngẫu nhiên các điểm theo đúng xác suất của chúng.

Do đó, sampling từ language model nghĩa là:

- Sinh các câu
- Mỗi câu được chọn theo xác suất mà mô hình gán cho nó

Kết quả:

- Câu có xác suất cao sẽ xuất hiện thường xuyên hơn
- Câu có xác suất thấp sẽ xuất hiện hiếm hơn

Ý tưởng này đã được đề xuất từ rất sớm bởi Shannon (1948).

---

## 2. Sampling trong trường hợp Unigram

Để hiểu rõ nhất, ta xét mô hình unigram.

Giả sử:

- Mỗi từ trong tiếng Anh chiếm một đoạn trên trục số từ 0 đến 1
- Độ dài đoạn tỉ lệ với xác suất của từ đó

Ví dụ:

- “the” chiếm một đoạn lớn
- “polyphonic” chiếm một đoạn rất nhỏ

Cách sinh từ:

1. Chọn một số ngẫu nhiên r trong khoảng [0,1]
2. Xem r rơi vào đoạn nào
3. In ra từ tương ứng với đoạn đó

Vì đoạn của từ phổ biến lớn hơn,
nên xác suất chọn trúng chúng cao hơn.

Ta lặp lại quá trình này:

- Sinh từng từ một
- Cho đến khi sinh ra token kết thúc câu `</s>`

Đây chính là cách sinh câu bằng unigram.

---

## 3. Sampling với Bigram

Với bigram, quá trình tương tự nhưng có điều kiện ngữ cảnh.

### Bước 1: Chọn bigram bắt đầu bằng `<s>`

Ta chọn ngẫu nhiên một bigram:

```

<s>, w

```

theo xác suất:
```
P(w | <s>)
```
---

### Bước 2: Tiếp tục theo ngữ cảnh

Giả sử từ vừa sinh là w.

Ta chọn tiếp một bigram bắt đầu bằng w:

```

w, w_next

```

theo xác suất:
```
P(w_next | w)
```
---

### Bước 3: Lặp lại

Tiếp tục quá trình:

- Dùng từ hiện tại làm ngữ cảnh
- Lấy phân phối xác suất điều kiện
- Chọn ngẫu nhiên theo phân phối đó

Dừng lại khi sinh ra `</s>`.

---

## 4. Ý nghĩa khi tăng bậc n

Khi tăng bậc n:

- Unigram → không có tính mạch lạc
- Bigram → có liên kết cục bộ
- Trigram → câu bắt đầu có cấu trúc tự nhiên hơn
- 4-gram → câu có thể rất giống dữ liệu gốc

Ngữ cảnh càng dài:

- Câu càng có vẻ “coherent” (mạch lạc)
- Mô hình càng phản ánh mạnh dữ liệu huấn luyện

---

## 5. Sampling giúp ta quan sát điều gì?

Sampling cho ta thấy:

- Mô hình đã học được mẫu nào trong dữ liệu
- Mô hình phụ thuộc vào training corpus đến mức nào
- Khi tăng n, mô hình dần ghi nhớ các chuỗi dài hơn

Đây là cầu nối dẫn sang phần tiếp theo:

- Khi n tăng quá cao,
- Mô hình có thể bắt đầu **overfit** dữ liệu huấn luyện.

---

## 6. Kết luận của phần 3.4

Sampling là công cụ trực quan để:

- Hiểu phân phối xác suất mà mô hình học được
- So sánh sự khác biệt giữa unigram, bigram, trigram
- Quan sát sự gia tăng tính mạch lạc khi tăng n

Phần này chuẩn bị nền tảng cho vấn đề:

Generalization vs. Overfitting trong phần 3.5.

---
# CHAPTER 3.5 – GENERALIZING VS. OVERFITTING THE TRAINING SET

---

## 1. N-gram phụ thuộc mạnh vào tập huấn luyện

Giống như nhiều mô hình thống kê khác, n-gram model phụ thuộc trực tiếp vào **training corpus**.

Hai hệ quả quan trọng:

1. Xác suất học được thường phản ánh những đặc điểm rất cụ thể của corpus huấn luyện.  
2. Khi tăng giá trị của n, mô hình sẽ mô tả training corpus ngày càng tốt hơn.

Điều này nghe có vẻ tích cực, nhưng lại tiềm ẩn vấn đề overfitting.

---

## 2. Tăng bậc n làm câu sinh ra “giống” dữ liệu hơn

Khi dùng phương pháp sampling để sinh câu từ các mô hình:

- Unigram
- Bigram
- Trigram
- 4-gram

huấn luyện trên Shakespeare, ta quan sát được:

- **Unigram**: không có tính mạch lạc, không có cấu trúc câu rõ ràng.
- **Bigram**: có tính liên kết cục bộ giữa các từ.
- **Trigram**: bắt đầu trông giống Shakespeare.
- **4-gram**: câu sinh ra giống Shakespeare đến mức có thể trích nguyên văn.

Ví dụ cụm:

> “It cannot be but so”

xuất hiện trực tiếp trong *King John*.

Điều này xảy ra vì:

- Tập Shakespeare không quá lớn  
  (N ≈ 884,647 từ, V ≈ 29,066 từ vựng)
- Ma trận xác suất n-gram cực kỳ thưa (sparse)

Số lượng khả năng:
```
- Bigram: V² ≈ 844 triệu  
- 4-gram: V⁴ ≈ 7 × 10¹⁷  
```
Do đó, khi đã chọn một 3-gram cụ thể,
khả năng lựa chọn từ tiếp theo rất hạn chế.

Mô hình thực chất đang ghi nhớ các chuỗi có sẵn trong dữ liệu.

Đây chính là biểu hiện của **overfitting**.

---

## 3. Phụ thuộc vào miền dữ liệu (Domain Dependence)

Tiếp theo, ta huấn luyện language model trên một corpus hoàn toàn khác:
**Wall Street Journal (WSJ)**.

Cả Shakespeare và WSJ đều là tiếng Anh.

Tuy nhiên, khi sinh câu từ hai mô hình:

- Hầu như không có sự trùng lặp n-gram.
- Ngay cả các cụm nhỏ cũng rất khác nhau.

WSJ chứa các cụm như:

- “stock market”
- “interest rates”
- “corporation”

Trong khi Shakespeare chứa:

- “thy”
- “thou”
- “hath”

Hai tập dữ liệu thuộc hai **phân phối xác suất khác nhau**.

Kết luận:

Nếu training set và test set khác biệt lớn,
mô hình thống kê sẽ gần như vô dụng trong việc dự đoán.

---

## 4. Hệ quả thực tế khi xây dựng language model

Để tránh vấn đề này:

- Training corpus phải cùng thể loại (genre) với nhiệm vụ.

Ví dụ:

- Dịch văn bản pháp lý → cần corpus pháp lý.
- Hệ thống hỏi–đáp → cần corpus câu hỏi.
- Xử lý mạng xã hội → cần dữ liệu mạng xã hội.

---

## 5. Khác biệt về phương ngữ và biến thể ngôn ngữ

Ngay cả trong cùng một ngôn ngữ,
sự khác biệt về phương ngữ cũng tạo ra phân phối khác nhau.

Ví dụ:

- African American English (AAE)
- Nigerian Pidgin

Các biến thể này có:

- Từ vựng riêng
- Mẫu n-gram riêng
- Cấu trúc riêng

Nếu training corpus không bao phủ các biến thể này,
mô hình sẽ hoạt động kém.

---

## 6. Vấn đề từ chưa từng thấy (OOV)

Có thể xảy ra trường hợp:

Một từ không xuất hiện trong training set,
nhưng xuất hiện trong test set.

Ví dụ: “Jurafsky”.

Trong thực tế hiện đại:

Ta không chạy mô hình trực tiếp trên “từ”,
mà trên **subword tokens**.

Với các thuật toán như BPE:

- Mỗi từ có thể được phân tách thành các đơn vị nhỏ hơn.
- Thậm chí thành từng ký tự nếu cần.

Do đó:

Test set sẽ không chứa token hoàn toàn chưa từng thấy.

---

## 7. Kết luận của phần 3.5

Phần này nhấn mạnh ba điểm cốt lõi:

1. N-gram model phụ thuộc mạnh vào training corpus.  
2. Tăng bậc n giúp mô hình khớp training data tốt hơn, nhưng dễ dẫn đến overfitting.  
3. Mô hình chỉ hoạt động hiệu quả khi training và test cùng phân phối dữ liệu.

Mục tiêu thực sự không phải là mô tả hoàn hảo training set,
mà là **generalization** — hoạt động tốt trên dữ liệu mới.


