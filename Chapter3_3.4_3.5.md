# 3.4 Sampling sentences from a language model

Một cách quan trọng để hình dung loại kiến thức mà một mô hình ngôn ngữ ẩn chứa chính là lấy mẫu (sampling) từ nó.

Việc lấy mẫu từ một phân phối có nghĩa là chọn các điểm dữ liệu ngẫu nhiên dựa trên khả năng xuất hiện (xác suất) của chúng. Do đó, lấy mẫu từ một mô hình ngôn ngữ — vốn đại diện cho một phân phối trên các câu — đồng nghĩa với việc tạo ra một số câu, trong đó mỗi câu được chọn theo xác suất mà mô hình đã xác định.

Vì vậy, chúng ta có nhiều khả năng tạo ra những câu mà mô hình cho là có xác suất cao, và ít có khả năng tạo ra những câu mà mô hình cho là có xác suất thấp.

![alt text](3.4.png)
>* Hình 1: Một hình ảnh minh họa về phân phối lấy mẫu để tạo ra các câu bằng cách lấy mẫu unigram lặp đi lặp lại. Thanh màu xanh đại diện cho tần suất tương đối của mỗi từ (chúng tôi đã sắp xếp chúng theo thứ tự từ xuất hiện nhiều nhất đến ít nhất, nhưng việc lựa chọn thứ tự này là tùy ý). 
Trục số hiển thị xác suất tích lũy. Nếu chúng ta chọn một con số ngẫu nhiên trong khoảng từ 0 đến 1, nó sẽ rơi vào một khoảng tương ứng với một từ nào đó. Kỳ vọng (xác suất) để con số ngẫu nhiên rơi vào các khoảng lớn hơn của một trong những từ phổ biến (như the, of, a) cao hơn nhiều so với việc rơi vào khoảng nhỏ của một trong những từ hiếm (như polyphonic). 

Kỹ thuật hình dung một mô hình ngôn ngữ thông qua việc lấy mẫu này đã được đề xuất từ rất sớm bởi Shannon (1948) cùng Miller và Selfridge (1950).Cách dễ nhất là hình dung cơ chế này hoạt động như thế nào đối với trường hợp unigram (mô hình đơn từ). Hãy tưởng tượng toàn bộ các từ trong tiếng Anh phủ kín một trục số từ 0 đến 1, trong đó mỗi từ chiếm một khoảng không gian tỉ lệ thuận với tần suất xuất hiện của nó. Hình 1 minh họa cho việc này bằng cách sử dụng một mô hình unigram được tính toán từ chính văn bản của cuốn sách này. Chúng ta chọn một giá trị ngẫu nhiên trong khoảng từ 0 đến 1, tìm điểm đó trên trục xác suất và in ra từ mà khoảng của nó bao hàm giá trị đã chọn. Chúng ta tiếp tục chọn các số ngẫu nhiên và tạo ra các từ cho đến khi ngẫu nhiên tạo ra được mã thông báo kết thúc câu (sentence-final token).Chúng ta có thể sử dụng cùng kỹ thuật này để tạo ra các bigram (mô hình cặp từ). Đầu tiên, ta tạo ra một bigram ngẫu nhiên bắt đầu bằng ký hiệu bắt đầu câu `<s>` (dựa trên xác suất bigram của nó). Giả sử từ thứ hai của bigram đó là $w$. Tiếp theo, chúng ta chọn một bigram ngẫu nhiên bắt đầu bằng $w$ (một lần nữa, được rút ra dựa trên xác suất bigram của nó), và cứ tiếp tục như vậy.

# 3.5 Generalizing vs. overfitting the training set
