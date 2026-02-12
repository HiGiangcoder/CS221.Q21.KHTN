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
$
P_{\text{Laplace}}(w_n|w_{n-1}) = \frac{C(w_{n-1}w_n) + 1}{C(w_{n-1}) + V} = \frac{C^*(w_{n-1}w_n)}{C(w_{n-1})}
$

$
C^*(w_{n-1}w_n) = \frac{[C(w_{n-1}w_n) + 1] \times C(w_{n-1})}{C(w_{n-1}) + V}
$
