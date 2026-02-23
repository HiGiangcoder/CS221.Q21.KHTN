# CHAPTER 3.4 – SAMPLING SENTENCES FROM A LANGUAGE MODEL

---

## 1. Mục tiêu của phần 3.4

Ở các phần trước, chúng ta đã:

- Xây dựng N-gram language model  
- Tính xác suất của câu  
- Đánh giá mô hình bằng perplexity  

Câu hỏi đặt ra là:

Nếu mô hình đã học được phân phối xác suất của các từ,
liệu ta có thể dùng nó để sinh ra câu mới không?

Câu trả lời là: Có.

Quá trình đó được gọi là **sampling**.

---

## 2. Sampling là gì?

Sampling là quá trình:

> Chọn từ tiếp theo dựa trên phân phối xác suất mà mô hình đã học.

Ví dụ:
```
P(love | I) = 0.7  
P(hate | I) = 0.3  
```
Khi sinh câu, ta không luôn chọn “love”.
Ta chọn ngẫu nhiên theo tỷ lệ 70% – 30%.

Điều này giúp:

- Câu sinh ra phản ánh đúng phân phối xác suất  
- Tạo được sự đa dạng  

---

## 3. Thuật toán sinh câu với Bigram Model

Quy trình gồm ba bước chính.

### Bước 1: Bắt đầu với ký hiệu bắt đầu câu `<s>`

Ví dụ:
```
P(I | <s>) = 0.6  
P(The | <s>) = 0.4  
```
Ta chọn một từ theo phân phối này.

---

### Bước 2: Sinh từ tiếp theo

Giả sử ta đã chọn “I”.

Ta tính:

P(w | I)

Ví dụ:
```
P(love | I) = 0.7  
P(hate | I) = 0.3  
```
Ta tiếp tục chọn một từ theo xác suất tương ứng.

---

### Bước 3: Lặp lại cho đến khi gặp `</s>`

Quy trình lặp lại:

- Dùng từ vừa sinh làm ngữ cảnh  
- Lấy phân phối xác suất mới  
- Chọn ngẫu nhiên  

Quá trình kết thúc khi sinh ra ký hiệu kết thúc câu.

Ví dụ:
```
<s> → I → love → NLP → </s>
```
---

## 4. Sampling và Greedy Decoding

Có hai chiến lược phổ biến để chọn từ tiếp theo.

### 4.1 Greedy

Luôn chọn từ có xác suất cao nhất.

Ưu điểm:
- Câu thường hợp lý hơn  

Nhược điểm:
- Thiếu đa dạng  
- Dễ lặp  

---

### 4.2 Sampling

Chọn từ theo phân phối xác suất.

Ưu điểm:
- Tạo nhiều câu khác nhau  
- Phản ánh đúng bản chất xác suất  

Nhược điểm:
- Có thể sinh câu kém tự nhiên  

---

## 5. Hạn chế của N-gram khi sinh câu

### 5.1 Ngữ cảnh ngắn

Bigram chỉ xét một từ trước đó.  
Trigram chỉ xét hai từ trước đó.

Mô hình không nắm được phụ thuộc dài hạn.

---

### 5.2 Không hiểu ngữ pháp sâu

Mô hình chỉ học xác suất, không hiểu cấu trúc ngữ pháp.

Ví dụ có thể sinh:

“The cat love I.”

Vì xác suất cục bộ có thể cao,
nhưng cấu trúc toàn câu sai.

---

## 6. Ý nghĩa của phần 3.4

Phần này cho thấy:

Language model là một **generative model**.

Nó không chỉ đánh giá câu,
mà còn có thể tạo câu mới dựa trên phân phối đã học.

Đây là nền tảng cho:

- Text generation  
- Neural language model  
- Transformer  

---

---

# CHAPTER 3.5 – GENERALIZING VS OVERFITTING THE TRAINING SET

---

## 1. Mục tiêu thực sự của Machine Learning

Mục tiêu của mô hình không phải là:

Giảm lỗi trên training set.

Mục tiêu là:

Hoạt động tốt trên dữ liệu mới.

Khả năng này gọi là **generalization**.

Generalization nghĩa là:
Mô hình có thể khái quát hóa sang dữ liệu chưa từng thấy.

---

## 2. Overfitting là gì?

Overfitting xảy ra khi:

- Training perplexity rất thấp  
- Test perplexity cao  

Điều này có nghĩa:

Mô hình đã học thuộc dữ liệu huấn luyện,
nhưng không học được quy luật tổng quát.

---

## 3. Overfitting trong N-gram Model

Khi ta tăng N:

Bigram → Trigram → 4-gram → 5-gram

Mô hình trở nên phức tạp hơn.
Số lượng tham số tăng rất nhanh.

Nếu dữ liệu không đủ lớn,
mô hình sẽ ghi nhớ các chuỗi cụ thể,
thay vì học cấu trúc chung.

---

## 4. Ví dụ minh họa

Training có câu:

“I love NLP very much.”

Mô hình 5-gram học:

P(much | I love NLP very)

Nhưng test có câu:

“I love NLP so much.”

Chuỗi này chưa từng xuất hiện trong training.

Kết quả:

Xác suất có thể bằng 0.

Đây là dấu hiệu của overfitting.

---

## 5. Underfitting

Ngược lại với overfitting là **underfitting**.

Xảy ra khi mô hình quá đơn giản.

Ví dụ:

Unigram model không sử dụng ngữ cảnh.

Hậu quả:

- Training kém  
- Test cũng kém  

---

## 6. Bias – Variance Tradeoff

Đây là nguyên lý quan trọng trong machine learning.

### Bias cao

Mô hình quá đơn giản  
→ Không học đủ cấu trúc  

### Variance cao

Mô hình quá phức tạp  
→ Nhạy với training data  
→ Dễ overfitting  

Ta cần cân bằng giữa bias và variance.

---

## 7. Phát hiện Overfitting

So sánh:

- Training perplexity  
- Test perplexity  

Nếu training rất thấp nhưng test cao,
mô hình đang overfit.

---

## 8. Cách giảm Overfitting

Một số giải pháp:

1. Giảm N  
2. Tăng kích thước dữ liệu  
3. Áp dụng smoothing  

Smoothing giúp:

- Tránh zero probability  
- Phân bổ xác suất hợp lý hơn  

---

## 9. Ý nghĩa của phần 3.5

Phần này nhấn mạnh rằng:

Không phải mô hình càng phức tạp càng tốt.

Điều quan trọng nhất là khả năng generalization,
tức là hoạt động tốt trên dữ liệu mới.
