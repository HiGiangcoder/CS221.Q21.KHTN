# Introduction

# 3.1 N-Grams
Mô hình xác suất, N gram là tính xác suất dựa trên n-1 từ trước đó

$$
P(w_{1:n}) = P(w_1)P(w_2|w_1)P(w_3|w_{1:2})\dots P(w_n|w_{1:n-1}) \\
= \prod_{k=1}^{n} P(w_k|w_{1:k-1})
$$
## 3.1.1 The Markov assumption
Xác suất của từ chỉ phụ thuộc vào từ trước đó, hay còn gọi là bigram

$$P(w_n|w_{1:n-1}) \approx P(w_n|w_{n-N+1:n-1})$$

$$
P(w_{1:n}) \approx \prod_{k=1}^{n} P(w_k | w_{k-1})\\
Bigram
$$

## 3.1.2 How to estimate probabilities
lỡ như $w_{n-1}$ đứng cuối thì sao

## 3.1.3 Dealing with scale in large n-gram models

vô cực gram

# 3.2 Evaluating Language Models: Training and Test Sets
Có 2 cách đánh giá: extrinsic and intricsic
Để đánh giá model cần có training set, development and test set.

# 3.3 Evaluating Language Models: Perplexity
Không thể so sánh xác suất của câu dài với câu ngắn được, vì câu càng dài thì P càng gần 0, nên dùng perplexity
$P(W) = P(w_1 w_2 \dots w_N)
     = \prod_{i=1}^{N} P(w_i \mid w_1, w_2, \dots, w_{i-1})
$

$$
% Công thức 1
\text{perplexity}(W) = P(w_1w_2\dots w_N)^{-\frac{1}{N}}
= \sqrt[N]{\frac{1}{P(w_1w_2\dots w_N)}}
$$
$$
% Công thức 2 (Sử dụng Chain Rule)
\text{perplexity}(W) = \sqrt[N]{\prod_{i=1}^{N} \frac{1}{P(w_i|w_1\dots w_{i-1})}}
$$

## 3.3.1 Perplexity as Weighted Average Branching Factor
Đưa ra ví dụ về red red blue

# 3.4 Sampling sentences from a language model
Gen ra sentence để kiểm tra câu có ý nghĩa không, từ đó đánh giá model
Gen dựa vào xác suất của Unigram, bigram, trigram,...

# 3.5 Generalizing vs. overfitting the training set
Khi tập train quá ít, mà dùng trigram 4-gram sẽ huấn luyện tốt trên train nhưng đó là học thuộc, không hiệu quả trên test

Các tập train khác nhau cho ra output khác nhau

Vì vậy khi huấn luyện model cần lựa tập train phù hợp với task

Cách xử lý khi test gặp word unseen: dựa trên subword tokens

Để tiện lợi thì chapter này dùng word thay cho token

# 3.6 Smoothing, Interpolation, and Backoff
Mô hình sẽ sai sót khi gặp những câu hợp lý nhưng chưa xuất hiện trong train, và gán xác suất nó thấp, thậm chí bằng 0

Nếu 1 xs=0 thì xs cả tập test bằng 0, điều này dẫn đến sai sót

Vì vậy cần có các biện pháp phòng tránh

## 3.6.1 Laplace Smoothing
Add tất cả thêm 1 để tránh xác suất bằng 0

$$
P_{\text{Laplace}}(w_n|w_{n-1}) = \frac{C(w_{n-1}w_n) + 1}{C(w_{n-1}) + V} = \frac{C^*(w_{n-1}w_n)}{C(w_{n-1})}
$$

$$
C^*(w_{n-1}w_n) = \frac{[C(w_{n-1}w_n) + 1] \times C(w_{n-1})}{C(w_{n-1}) + V}
$$

Thay ratio giữa new và old thay đổi quá nhiều
## 3.6.2 Add-k smoothing
Thay vì add 1 thì add k (k có thể < 1)

Hiệu quả trong 1 số task (bao gồm phân loại text). Nhưng không hiệu quả trong language model vì nó có phương sai kém và tạo ra mức giảm không phù hợp

$$
P_{\text{Add-k}}^*(w_n|w_{n-1}) = \frac{C(w_{n-1}w_n) + k}{C(w_{n-1}) + kV}
$$

## 3.6.3 Language Model Interpolation
Nếu dùng trigram mà xác suất =0, có thể dùng bigram thay thế, tương tự có thể dùng unigram để thay thế

Điều đó có nghĩa là ta dùng cách tổ hợp có trọng số giữa các xác suất n gram

$$
\begin{equation}
\hat{P}(w_n | w_{n-2} w_{n-1}) = 
\begin{aligned}
    & \lambda_1 P(w_n) \\
    & + \lambda_2 P(w_n | w_{n-1}) \\
    & + \lambda_3 P(w_n | w_{n-2} w_{n-1})
\end{aligned}
\end{equation}
$$

$$
\begin{aligned}
\hat{P}(w_n | w_{n-2} w_{n-1}) = & \lambda_1(w_{n-2:n-1}) P(w_n) \\
& + \lambda_2(w_{n-2:n-1}) P(w_n | w_{n-1}) \\
& + \lambda_3(w_{n-2:n-1}) P(w_n | w_{n-2} w_{n-1})
\end{aligned}
$$

Gồm có simple interpolation và conditional interpolation

Tính các lambda bằng held-out corpus

## 3.6.4 Stupid Backoff

$$
S(w_i \mid w_{i-N+1:i-1}) =
\begin{cases}
\dfrac{\mathrm{count}(w_{i-N+1:i})}
      {\mathrm{count}(w_{i-N+1:i-1})},
& \text{if } \mathrm{count}(w_{i-N+1:i}) > 0 \\[10pt] //
\lambda \, S(w_i \mid w_{i-N+2:i-1}),
& \text{otherwise}
\end{cases}
$$

# 3.7 Advanced: Perplexity’s Relation to Entropy
$$
H(X) = - \sum_{x \in \chi} p(x) \log_2 p(x)
$$

Entropy trong language model:

$$
\frac{1}{n} H(w_{1:n}) = - \frac{1}{n} \sum_{w_{1:n} \in L} p(w_{1:n}) \log p(w_{1:n}) 
$$

to measure the true entropy of a language, we need to consider sequences of 
infinite length:

$$
\begin{aligned}
H(L) &= \lim_{n \to \infty} \frac{1}{n} H(w_{1:n}) \\
&= - \lim_{n \to \infty} \frac{1}{n} \sum_{W \in L} p(w_{1:n}) \log p(w_{1:n})
\end{aligned}
$$

Khi có 1 câu đủ dài thì được công thức sau

$$
H(L) = \lim_{n \to \infty} -\frac{1}{n} \log p(w_{1:n})
$$

Các model n-gram đều stationary, nhưng natural language không có stationary