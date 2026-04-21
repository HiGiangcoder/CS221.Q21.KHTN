# Script thuyết trình: Contextual Embeddings, Word Sense, và Word Similarity

## Mở đầu

Hôm nay em sẽ trình bày về ba ý chính trong mục này:

1. **Contextual Embeddings**
2. **Contextual Embeddings và Word Sense**
3. **Contextual Embeddings và Word Similarity**

Ý tưởng xuyên suốt là:  
khác với **static embeddings** như word2vec,  
**contextual embeddings** biểu diễn nghĩa của từ **phụ thuộc vào ngữ cảnh**.  
Nhờ đó, cùng một từ nhưng ở các câu khác nhau có thể có các vector biểu diễn khác nhau.

---

# Slide 1 — 9.3 Contextual Embeddings

## Nội dung tôi sẽ nói

Ở slide này, ý chính là mỗi token đầu vào sau khi đi qua một **pretrained language model** như BERT sẽ tạo ra một vector đầu ra.  
Vector này được gọi là **contextual embedding**, vì nó biểu diễn nghĩa của token đó **trong ngữ cảnh cụ thể của câu**.

Ví dụ, với chuỗi đầu vào $x_1, \ldots, x_n$,  
ta có thể dùng vector ở lớp cuối $h_i^L$ để biểu diễn token $x_i$.

Ngoài ra, trong thực hành người ta không nhất thiết chỉ dùng lớp cuối.  
Một cách phổ biến là lấy trung bình 4 lớp cuối:

$$
\frac{1}{4}(h_i^L + h_i^{L-1} + h_i^{L-2} + h_i^{L-3})
$$

Mục đích là để thu được biểu diễn ổn định hơn, giàu thông tin hơn.

Điểm quan trọng cần nhớ là:  
**contextual embeddings không biểu diễn từ theo kiểu “mỗi từ một vector cố định”, mà biểu diễn mỗi lần xuất hiện của từ trong ngữ cảnh thực tế.**

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Tại sao không chỉ dùng lớp cuối?”
Có hai lý do chính:

- lớp cuối thường rất gắn với mục tiêu pretraining cụ thể
- các lớp gần cuối có thể giữ thêm thông tin cú pháp và ngữ nghĩa hữu ích

Vì vậy, trung bình nhiều lớp cuối thường cho biểu diễn ổn định hơn trong các tác vụ downstream.

### Nếu bị hỏi: “Contextual embedding khác gì hidden state?”
Về mặt thực hành, ở đây có thể xem contextual embedding chính là hidden representation của token tại một lớp nào đó, thường là lớp cuối hoặc tổ hợp các lớp cuối.  
Khác biệt nằm ở **cách dùng**: khi ta dùng hidden representation như một biểu diễn nghĩa của token trong ngữ cảnh, ta gọi nó là contextual embedding.

### Nếu bị hỏi: “Token và word có giống nhau không?”
Không hoàn toàn.  
Mô hình như BERT làm việc trên **token**, có thể là cả từ hoặc một phần của từ.  
Nhưng trong bài học này, khi nói về nghĩa của từ trong ngữ cảnh, ta thường mô tả ở mức khái niệm là “word”, dù thực tế mô hình xử lý token.

---

# Slide 2 — Contextual Embeddings vs. Static Embeddings

## Nội dung tôi sẽ nói

Ở đây ta so sánh **static embeddings** và **contextual embeddings**.

Với **static embeddings** như word2vec, mỗi từ trong từ điển chỉ có **một vector cố định**.  
Ví dụ từ “bank” sẽ luôn có một vector, bất kể nó mang nghĩa “ngân hàng” hay “bờ sông”.

Ngược lại, với **contextual embeddings**, cùng một từ nhưng xuất hiện trong các câu khác nhau sẽ có các vector khác nhau.  
Nói cách khác:

- static embeddings biểu diễn **word type**
- contextual embeddings biểu diễn **word instance trong ngữ cảnh**

Đây là lý do contextual embeddings mạnh hơn khi xử lý các bài toán cần hiểu nghĩa theo ngữ cảnh.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Vậy contextual embeddings có thay thế hoàn toàn static embeddings không?”
Trong nhiều tác vụ hiện đại, contextual embeddings hiệu quả hơn rõ rệt.  
Tuy nhiên static embeddings vẫn có giá trị vì:

- nhẹ hơn
- dễ huấn luyện và sử dụng
- phù hợp cho các hệ thống nhỏ hoặc làm baseline

### Nếu bị hỏi: “Word2vec có hoàn toàn vô dụng với từ đa nghĩa không?”
Không.  
Nó vẫn học được một vector phản ánh “nghĩa trung bình” của từ qua nhiều ngữ cảnh.  
Nhưng nó không tách riêng được từng nghĩa cụ thể như contextual embeddings.

---

# Slide 3 — 9.3.1 Contextual Embeddings and Word Sense

## Nội dung tôi sẽ nói

Phần này nói về **word sense**, tức là các nghĩa khác nhau của một từ.

Nhiều từ là **đa nghĩa**.  
Ví dụ:

- **mouse** có thể là con chuột hoặc chuột máy tính
- **bank** có thể là ngân hàng hoặc bờ sông

Trong ngôn ngữ học, khi một từ có nhiều nghĩa liên quan, ta gọi hiện tượng này là **polysemy**.

Mỗi nghĩa cụ thể của từ được gọi là một **word sense**.  
Ta có thể ký hiệu như:

- $bank^1$, $bank^2$
- $mouse^1$, $mouse^2$

Các sense này thường được liệt kê trong các tài nguyên từ vựng như **WordNet**.

Điểm quan trọng là:  
**ngữ cảnh sẽ giúp xác định nghĩa đúng của từ.**  
Và contextual embeddings là công cụ rất phù hợp để mô hình hóa điều này.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Word sense có phải luôn rời rạc rõ ràng không?”
Không hẳn.  
Trong từ điển hoặc WordNet, sense thường được biểu diễn như các mục rời rạc.  
Nhưng trong thực tế ngôn ngữ, nghĩa có thể liên tục, chồng lấn, và khó chia ranh giới tuyệt đối.

### Nếu bị hỏi: “Polysemy khác homonymy thế nào?”
- **Polysemy**: một từ có nhiều nghĩa có liên hệ với nhau
- **Homonymy**: một từ có nhiều nghĩa không liên hệ

Trong phạm vi bài học này, trọng tâm là việc một từ có thể có nhiều sense và cần dùng ngữ cảnh để phân biệt.

### Nếu bị hỏi: “WordNet dùng để làm gì ở đây?”
WordNet cung cấp một tập nghĩa chuẩn hóa cho các từ.  
Trong bài toán WSD, nó có thể đóng vai trò là “danh sách sense ứng viên” để ta chọn nghĩa phù hợp nhất.

---

# Slide 4 — Figure 9.6: Geometric View of Word Meaning

## Nội dung tôi sẽ nói

Hình này minh họa rất trực quan cách contextual embeddings biểu diễn nghĩa.

Mỗi chấm xanh là một **BERT contextual embedding** của từ **die** trong một câu khác nhau.  
Các embedding này được chiếu từ không gian nhiều chiều xuống 2 chiều bằng **UMAP** để ta dễ quan sát.

Điểm quan trọng là các cách dùng khác nhau của từ **die** tạo thành các **cụm riêng**.  
Ví dụ có thể tách ra:

- nghĩa là **mạo từ tiếng Đức**
- nghĩa tiếng Anh liên quan đến **single die**
- nghĩa tiếng Anh của động từ **to die**

Điều này cho thấy embedding space có khả năng phản ánh nghĩa theo ngữ cảnh một cách hình học:  
những cách dùng gần nghĩa thì nằm gần nhau,  
những cách dùng khác nghĩa thì tách ra thành các cụm.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “UMAP là gì?”
UMAP là một kỹ thuật giảm chiều.  
Nó giúp chiếu dữ liệu từ không gian nhiều chiều xuống 2D hoặc 3D để trực quan hóa, trong khi vẫn cố gắng giữ cấu trúc lân cận của dữ liệu.

### Nếu bị hỏi: “Có phải các sense luôn tách thành cụm rất rõ như hình không?”
Không phải lúc nào cũng rõ như vậy.  
Việc cụm có tách đẹp hay không phụ thuộc vào:

- chất lượng embedding
- độ khác biệt giữa các nghĩa
- dữ liệu
- phương pháp giảm chiều

Hình này chủ yếu dùng để minh họa trực quan cho ý tưởng.

### Nếu bị hỏi: “Vậy embeddings có phải là một dạng từ điển nghĩa không?”
Không.  
Embeddings không liệt kê nghĩa theo kiểu rời rạc như từ điển.  
Chúng biểu diễn nghĩa trong **không gian liên tục nhiều chiều**.  
Ta có thể phân cụm hoặc suy ra nghĩa, nhưng nghĩa không nhất thiết được chia cắt hoàn toàn thành các mục rời rạc.

---

# Slide 5 — Word Sense Disambiguation (WSD)

## Nội dung tôi sẽ nói

Từ đây ta đi đến bài toán gọi là **Word Sense Disambiguation**, viết tắt là **WSD**.

Đây là nhiệm vụ chọn **nghĩa đúng của một từ trong ngữ cảnh**.

Input của WSD gồm:

- một từ xuất hiện trong câu
- một tập các sense ứng viên, ví dụ từ WordNet

Output là sense phù hợp nhất với ngữ cảnh đó.

Ví dụ với từ **bass**, hệ thống cần xác định xem trong câu hiện tại nó đang mang nghĩa:

- nhạc cụ
- giọng trầm
- hay một loài cá

WSD rất quan trọng vì nhiều từ chỉ có thể hiểu đúng khi đặt trong ngữ cảnh.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “WSD khác POS tagging thế nào?”
- **POS tagging** xác định loại từ: danh từ, động từ, tính từ, ...
- **WSD** xác định nghĩa cụ thể của từ

Hai từ cùng là danh từ vẫn có thể có nhiều nghĩa khác nhau, nên POS tagging không giải quyết được WSD.

### Nếu bị hỏi: “WSD có còn quan trọng trong thời đại LLM không?”
Có.  
Dù mô hình lớn có thể ngầm xử lý nghĩa theo ngữ cảnh rất tốt, WSD vẫn quan trọng:

- về mặt phân tích ngôn ngữ
- về khả năng giải thích mô hình
- trong các hệ thống cần mapping tới sense inventory rõ ràng như WordNet

---

# Slide 6 — Figure 9.7 và cách dùng contextual embeddings cho WSD

## Nội dung tôi sẽ nói

Hình này mô tả bài toán WSD ở mức tổng quát:  
từ các từ đầu vào $x$, ta ánh xạ sang các sense $y$.

Một cách làm hiệu quả là dùng **contextual embeddings**.  
Cụ thể:

- mỗi lần xuất hiện của một từ trong ngữ cảnh sẽ có một embedding
- với mỗi sense $s$, ta lấy trung bình các embeddings của những token đã được gán sense đó để tạo thành một **sense embedding**

Công thức là:

$$
\mathbf{v}_s = \frac{1}{n} \sum_i \mathbf{v}_i
$$

Trong đó $\mathbf{v}_i$ là các contextual embeddings của những token thuộc cùng sense $s$.

Ở thời điểm test, với một token đích $t$,  
ta tính embedding của nó rồi chọn sense có **cosine similarity** cao nhất với embedding đó:

$$
\text{sense}(t) = \arg\max_{s \in senses(t)} \cos(\mathbf{t}, \mathbf{v}_s)
$$

Nói ngắn gọn, đây là một cách phân loại kiểu **nearest neighbor trong embedding space**.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Tại sao lại lấy trung bình embeddings để tạo sense embedding?”
Vì nhiều lần xuất hiện của cùng một sense sẽ không hoàn toàn giống nhau.  
Lấy trung bình giúp tạo ra một đại diện tổng quát hơn, bớt phụ thuộc vào từng câu đơn lẻ.

### Nếu bị hỏi: “Tại sao dùng cosine similarity chứ không phải Euclidean distance?”
Cosine similarity đo mức độ giống nhau về **hướng** của vector, thường phù hợp hơn khi so sánh embedding vì độ lớn vector có thể ít quan trọng hơn hướng biểu diễn nghĩa.

### Nếu bị hỏi: “Nearest neighbor có phải là cách tốt nhất không?”
Không hẳn, nhưng đây là một baseline mạnh và trực quan.  
Ưu điểm là đơn giản, dễ giải thích, và phù hợp với ý tưởng embedding space.

---

# Slide 7 — 9.3.2 Contextual Embeddings and Word Similarity

## Nội dung tôi sẽ nói

Ở phần này, trọng tâm là đo **độ tương tự ngữ nghĩa** giữa các từ hoặc các lần xuất hiện của từ trong ngữ cảnh.

Trong Chapter 5, ta đã gặp ý tưởng dùng **cosine similarity** để đo độ giống nhau giữa các vector từ.  
Với contextual embeddings, ý tưởng này được mở rộng:  
ta không chỉ so sánh hai từ theo kiểu từ điển,  
mà có thể so sánh **hai instance cụ thể trong hai ngữ cảnh cụ thể**.

Ý nghĩa hình học cũng rất rõ:  
một biểu diễn của từ trong một ngữ cảnh cụ thể sẽ gần hơn với các instance khác cùng nghĩa với nó.

Vì vậy, contextual embeddings rất phù hợp cho bài toán **word similarity in context**.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Word similarity và WSD liên hệ với nhau thế nào?”
Chúng liên hệ chặt chẽ.  
Nếu embedding của hai instance gần nhau trong không gian vector, điều đó gợi ý rằng chúng có thể mang nghĩa gần nhau hoặc cùng sense.  
Do đó, đo similarity có thể hỗ trợ WSD.

### Nếu bị hỏi: “Có thể so sánh hai instance của cùng một từ ở hai câu khác nhau không?”
Có.  
Đó chính là một ưu điểm lớn của contextual embeddings: cùng một từ ở hai câu khác nhau có thể được so sánh trực tiếp theo nghĩa thực tế mà nó mang trong từng câu.

---

# Slide 8 — Figure 9.8: Nearest-Neighbor for WSD

## Nội dung tôi sẽ nói

Hình này minh họa cụ thể hơn cách nearest-neighbor hoạt động trong WSD.

Các điểm màu xanh lá là các **sense embeddings** đã được tiền tính cho các nghĩa khác nhau của một từ, ở đây là các nghĩa khác nhau của **find**.

Sau đó, với một từ đích trong câu, ví dụ **found**,  
ta cho câu đi qua encoder để lấy **contextual embedding** của token này.

Embedding đó sẽ được so sánh với các sense embeddings đã có.  
Sense gần nhất, tức có cosine similarity cao nhất, sẽ được chọn.

Điểm quan trọng của hình là nó nối trực tiếp ba ý:

1. contextual embeddings biểu diễn nghĩa theo ngữ cảnh  
2. similarity đo sự gần nhau trong embedding space  
3. nearest-neighbor dùng similarity để chọn sense phù hợp

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Tại sao từ trong câu là ‘found’ mà sense lại là của ‘find’?”
Vì **found** là một dạng biến hình của động từ **find**.  
Trong WordNet hay sense inventory, các sense thường gắn với lemma hoặc base form, không phải mọi dạng biến hình.

### Nếu bị hỏi: “Sense embedding được tiền tính từ đâu?”
Nó được tính từ dữ liệu có gán nhãn sense, ví dụ như SemCor hoặc SenseEval.  
Mỗi lần xuất hiện của từ đã biết sense sẽ tạo ra một embedding, sau đó các embedding cùng sense được trung bình lại.

---

# Slide 9 — A Problem: Anisotropy

## Nội dung tôi sẽ nói

Tuy contextual embeddings rất hữu ích, nhưng khi dùng trực tiếp với cosine similarity thì có một vấn đề quan trọng: **anisotropy**.

Anisotropy nghĩa là các vector embedding có xu hướng dồn về một số hướng chung trong không gian.  
Khi đó, cosine similarity giữa các từ khác nhau cũng có thể rất cao, dù nghĩa của chúng không thực sự gần nhau.

Nói cách khác, embedding space không phân bố đều theo mọi hướng.  
Điều này làm cho cosine similarity bị méo và không còn phản ánh tốt độ tương tự ngữ nghĩa.

Một cách trực quan, trong không gian lý tưởng, các vector nên phân bố tương đối đều.  
Nhưng ở đây, nhiều vector lại bị chi phối bởi một số chiều nổi trội.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Isotropic và anisotropic khác nhau thế nào?”
- **Isotropic**: các vector phân bố tương đối đều theo các hướng
- **Anisotropic**: các vector tập trung theo một số hướng nhất định

Trong embedding space, isotropy thường giúp cosine similarity có ý nghĩa tốt hơn.

### Nếu bị hỏi: “Tại sao contextual embeddings lại dễ bị anisotropy?”
Vì quá trình huấn luyện của mô hình ngôn ngữ có thể khiến thông tin tập trung mạnh vào một số chiều.  
Khi đó, nhiều vector có thành phần lớn giống nhau trên các chiều này, làm tăng cosine similarity một cách giả tạo.

### Nếu bị hỏi: “Rogue dimensions là gì?”
Đó là một số chiều có giá trị rất lớn và phương sai cao, khiến chúng chi phối việc đo similarity.  
Chúng làm cho không gian embedding bị lệch hướng.

---

# Slide 10 — Standardizing Embeddings

## Nội dung tôi sẽ nói

Để giảm anisotropy, một giải pháp là **standardization**, tức chuẩn hóa embeddings theo từng chiều bằng **z-score**.

Với một tập embeddings $C$, ta tính:

- vector trung bình:

$$
\mu = \frac{1}{|C|} \sum_{x \in C} x
$$

- độ lệch chuẩn theo từng chiều:

$$
\sigma = \sqrt{\frac{1}{|C|}\sum_{x \in C}(x-\mu)^2}
$$

Sau đó, mỗi vector $x$ được thay bằng:

$$
z = \frac{x-\mu}{\sigma}
$$

Ý tưởng ở đây là loại bỏ ảnh hưởng của các chiều có biên độ quá lớn và đưa các chiều về cùng thang đo.  
Nhờ đó, embeddings trở nên gần isotropic hơn, và cosine similarity phản ánh ngữ nghĩa tốt hơn.

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Chuẩn hóa này thực hiện theo từng vector hay toàn bộ tập vector?”
Ta tính $\mu$ và $\sigma$ trên **toàn bộ tập embeddings**, theo từng chiều.  
Sau đó chuẩn hóa từng vector dựa trên các thống kê đó.

### Nếu bị hỏi: “Tại sao z-score giúp giảm anisotropy?”
Vì z-score đưa từng chiều về trung bình 0 và phương sai 1.  
Nhờ đó, các chiều quá lớn sẽ bớt chi phối, và không gian vector cân bằng hơn.

### Nếu bị hỏi: “Chuẩn hóa có giải quyết hết mọi vấn đề không?”
Không.  
Sách cũng lưu ý rằng dù standardization giúp ích, cosine similarity vẫn còn những hạn chế, đặc biệt với các từ rất thường xuyên.

---

# Slide 11 — Limitation và kết luận phần 9.3.2

## Nội dung tôi sẽ nói

Cuối cùng, dù standardization giúp cải thiện việc đo similarity, cosine similarity vẫn chưa hoàn hảo.  
Một hạn chế được nhắc tới là nó có thể **đánh giá thấp** mức độ tương tự ngữ nghĩa của các từ xuất hiện rất thường xuyên.

Tóm lại, phần này cho thấy:

- contextual embeddings cho phép biểu diễn nghĩa của từ theo ngữ cảnh
- nhờ đó ta có thể xử lý tốt hơn các hiện tượng đa nghĩa
- WSD có thể được thực hiện bằng nearest-neighbor trong embedding space
- similarity giữa embeddings là công cụ trung tâm
- nhưng cần chú ý đến hình học của không gian embedding, đặc biệt là anisotropy

## Dự phòng nếu bị hỏi

### Nếu bị hỏi: “Thông điệp lớn nhất của toàn bộ mục 9.3 là gì?”
Thông điệp lớn nhất là:  
**nghĩa của từ không nên được biểu diễn như một vector cố định duy nhất; nghĩa cần được gắn với ngữ cảnh.**  
Contextual embeddings là cách biểu diễn hiện đại và hiệu quả cho quan điểm này.

### Nếu bị hỏi: “WSD có phải là mục tiêu chính của contextual embeddings không?”
Không.  
Contextual embeddings là một biểu diễn tổng quát, có thể dùng cho rất nhiều tác vụ.  
WSD chỉ là một ví dụ rất điển hình để thấy rõ sức mạnh của việc mô hình hóa nghĩa theo ngữ cảnh.

---

# Kết thúc bài trình bày

## Nội dung tôi sẽ nói

Em xin kết thúc phần trình bày tại đây.

Tóm lại, từ static embeddings đến contextual embeddings là một bước chuyển rất quan trọng trong NLP.  
Thay vì gán một vector cố định cho mỗi từ, mô hình hiện đại biểu diễn từng lần xuất hiện của từ theo ngữ cảnh cụ thể.  
Điều này giúp xử lý tốt hơn các bài toán về nghĩa của từ, đặc biệt là **word sense** và **word similarity in context**.

Cảm ơn thầy/cô và các bạn đã lắng nghe.

---

# Câu hỏi xoáy thường gặp và cách trả lời ngắn

## 1. “Nếu contextual embeddings tốt như vậy, tại sao còn cần WordNet?”
WordNet cho ta một **sense inventory rời rạc, có thể diễn giải được**.  
Contextual embeddings mạnh về biểu diễn liên tục, còn WordNet mạnh về mặt chuẩn hóa tri thức ngôn ngữ.  
Hai hướng này bổ sung cho nhau.

## 2. “Embeddings là continuous, còn word sense là discrete. Có mâu thuẫn không?”
Không mâu thuẫn.  
Embeddings cung cấp không gian liên tục của nghĩa, còn word sense là một cách chia rời rạc để tiện phân tích và gán nhãn.  
WSD chính là cầu nối giữa hai cách nhìn đó.

## 3. “Tại sao cosine similarity lại phổ biến đến vậy?”
Vì nó đơn giản, hiệu quả, và tập trung vào hướng của vector thay vì độ lớn.  
Trong nhiều bài toán embedding, hướng của vector thường phản ánh ngữ nghĩa tốt hơn.

## 4. “Có phải cluster trong embedding space tương ứng đúng 1-1 với word senses không?”
Không nhất thiết.  
Có trường hợp một sense trải rộng, chồng lấn, hoặc nhiều sense gần nhau.  
Cluster chỉ là một trực giác hữu ích, không phải lúc nào cũng là phân hoạch hoàn hảo.

## 5. “BERT có tự làm WSD không?”
BERT không được huấn luyện chuyên biệt chỉ cho WSD, nhưng nó học ra các contextual embeddings đủ mạnh để hỗ trợ WSD rất hiệu quả.

## 6. “Điểm khác biệt quan trọng nhất giữa Chapter 5 và phần này là gì?”
Chapter 5 tập trung vào **static representations** như word2vec.  
Phần này chuyển sang **context-sensitive representations**, nơi nghĩa của từ thay đổi theo câu cụ thể.