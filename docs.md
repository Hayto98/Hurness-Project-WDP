# TÀI LIỆU MÔ TẢ CHI TIẾT DỰ ÁN
## HỆ THỐNG THEO DÕI XU HƯỚNG NGHIÊN CỨU KHOA HỌC
*(Scientific Research Trend Tracking System)*

---

### 1. GIỚI THIỆU DỰ ÁN
**Hệ thống theo dõi xu hướng nghiên cứu khoa học (Scientific Research Trend Tracking System)** là một nền tảng hỗ trợ sinh viên, giảng viên và nhà nghiên cứu trong việc tìm kiếm, theo dõi và phân tích xu hướng nghiên cứu dựa trên dữ liệu từ các nguồn học thuật uy tín.

Thay vì chỉ cung cấp danh sách bài báo như các công cụ tìm kiếm học thuật truyền thống, hệ thống tập trung vào việc phân tích dữ liệu nghiên cứu để giúp người dùng nhận biết những chủ đề đang phát triển, các công nghệ mới nổi, các lĩnh vực có tiềm năng nghiên cứu và những khoảng trống nghiên cứu (Research Gap). Từ đó, người dùng có thể lựa chọn hướng nghiên cứu phù hợp và đưa ra quyết định dựa trên dữ liệu thay vì cảm tính.

---

### 2. BỐI CẢNH DỰ ÁN
Hiện nay, số lượng bài báo khoa học được công bố mỗi năm tăng rất nhanh trên nhiều lĩnh vực khác nhau như Trí tuệ nhân tạo (AI), Khoa học dữ liệu (Data Science), Y tế, Robot, Internet vạn vật (IoT) hay An ninh mạng. Việc theo dõi và tổng hợp thông tin từ nhiều nguồn học thuật khác nhau trở nên vô cùng khó khăn đối với sinh viên và nhà nghiên cứu.

Các nền tảng hiện có chủ yếu hỗ trợ tìm kiếm tài liệu thô nhưng chưa cung cấp khả năng phân tích sâu về xu hướng nghiên cứu, chưa khám phá được mối quan hệ bản đồ giữa các chủ đề hay phát hiện những lĩnh vực nghiên cứu còn ít được khai thác. Do đó, việc xây dựng một hệ thống có khả năng tổng hợp dữ liệu nghiên cứu, phân tích xu hướng xu thế và trực quan hóa kết quả nhằm hỗ trợ quá trình lựa chọn đề tài và xây dựng định hướng nghiên cứu là vô cùng cấp thiết.

---

### 3. MỤC TIÊU CỦA HỆ THỐNG
Hệ thống được thiết kế và xây dựng nhằm đạt được các mục tiêu cốt lõi sau:
* **Thu thập và quản lý:** Tự động hóa quy trình thu thập và quản lý dữ liệu nghiên cứu từ nhiều nguồn học thuật đáng tin cậy trên thế giới.
* **Xây dựng kho dữ liệu:** Thiết lập một **Research Corpus** làm nguồn dữ liệu trung tâm, chuẩn hóa phục vụ cho các thuật toán phân tích chuyên sâu.
* **Tìm kiếm thông minh:** Hỗ trợ tìm kiếm bài báo khoa học nâng cao theo nhiều tiêu chí đa dạng và linh hoạt.
* **Phân tích xu hướng:** Phân tích xu hướng nghiên cứu dựa trên định lượng số lượng công bố theo dòng thời gian.
* **Gợi ý học thuật:** Gợi ý các từ khóa, thực thể và chủ đề nghiên cứu có liên quan chặt chẽ với nhau.
* **Phát hiện khoảng trống:** Định vị các hướng nghiên cứu mới nổi và các khoảng trống nghiên cứu (Research Gap) tiềm năng.
* **Trực quan hóa:** Trực quan hóa toàn bộ dữ liệu bằng biểu đồ, đồ thị động giúp người dùng dễ dàng quan sát sự dịch chuyển của các lĩnh vực.
* **Cá nhân hóa:** Hỗ trợ quản lý thư viện tài liệu cá nhân, lưu trữ và theo dõi các chủ đề học thuật quan tâm.

---

### 4. ĐỐI TƯỢNG SỬ DỤNG (ACTORS)
Hệ thống hướng tới việc phục vụ toàn diện cho 4 nhóm đối tượng chính trong môi trường học thuật:
1. **Sinh viên:**
   * Tìm kiếm tài liệu tham khảo chính thống, nhanh chóng.
   * Khám phá các chủ đề nghiên cứu phù hợp với năng lực và sở thích.
   * Theo dõi các xu hướng công nghệ mới nổi phục vụ làm đồ án, luận văn.
2. **Giảng viên:**
   * Theo dõi sự phát triển tổng thể của các lĩnh vực nghiên cứu chuyên sâu.
   * Hỗ trợ, định hướng và dẫn dắt sinh viên lựa chọn đề tài tốt nghiệp có tính thực tiễn cao.
   * Phân tích mức độ phổ biến, mức độ bão hòa của các chủ đề nghiên cứu.
3. **Nhà nghiên cứu:**
   * Khám phá các hướng đi mới, mang tính tiên phong.
   * Phân tích sâu sắc sự tiến hóa của các công nghệ cốt lõi.
   * Xác định chính xác các khoảng trống nghiên cứu để thực hiện đề tài cấp cơ sở/cấp bộ.
   * Theo dõi các công trình mới nhất từ các đồng nghiệp thuộc cùng lĩnh vực chuyên môn.
4. **Quản trị viên (Admin):**
   * Quản lý thông tin và phân quyền người dùng hệ thống.
   * Quản lý các cổng kết nối nguồn dữ liệu nghiên cứu đầu vào.
   * Cấu hình, bảo trì và tối ưu hóa Research Corpus.
   * Theo dõi nhật ký hoạt động nhằm duy trì tính ổn định của hệ thống.

---

### 5. PHẠM VI DỰ ÁN (SCOPE OF PROJECT)

Dưới đây là bảng phân định chi tiết phạm vi tính năng được triển khai (In-Scope) và các giới hạn loại trừ (Out-of-Scope) của dự án:

| STT | Phân nhóm chức năng | Phạm vi đáp ứng (IN SCOPE) | Phạm vi loại trừ (OUT OF SCOPE) |
| :--- | :--- | :--- | :--- |
| **1** | **Thu thập & Quản lý dữ liệu** | - Kết nối và thu thập dữ liệu từ các nguồn học thuật uy tín toàn cầu: *OpenAlex, Semantic Scholar, Crossref, arXiv, IEEE Xplore, ACM Digital Library*.<br>- Chuẩn hóa, lưu trữ và cập nhật dữ liệu **Research Corpus** bao gồm các trường thông tin: tiêu đề, tóm tắt (abstract), từ khóa, tác giả, năm xuất bản, nguồn công bố, DOI. | - **Không** cung cấp nội dung toàn văn (full-text) của bài báo (chỉ lưu trữ metadata phục vụ phân tích). Việc truy cập toàn văn phụ thuộc vào bản quyền của nhà xuất bản (Publisher). |
| **2** | **Nội dung & Dịch thuật** | - Lưu trữ và xử lý ngôn ngữ tự nhiên trên metadata gốc (chủ yếu là tiếng Anh). | - **Không** tích hợp tính năng dịch thuật nội dung bài báo hoặc abstract sang các ngôn ngữ khác. |
| **3** | **Tìm kiếm & Khám phá** | - Cho phép tìm kiếm nâng cao theo từ khóa, tiêu đề, tác giả, lĩnh vực, năm công bố và bộ lọc kết hợp.<br>- Đề xuất, gợi ý từ khóa và chủ đề liên quan dựa trên cấu trúc liên kết dữ liệu trong Research Corpus. | - **Không** hỗ trợ tự động tạo danh mục tài liệu tham khảo hoặc trích dẫn theo định dạng chuẩn (như APA, IEEE, Harvard, MLA...). |
| **4** | **Sáng tạo nội dung** | - Đề xuất và gợi ý tên đề tài hoặc từ khóa mang tính chất định hướng nghiên cứu cho người dùng. | - **Không** ứng dụng AI để tự động soạn thảo, viết nội dung bài báo khoa học hay luận văn thay cho người dùng. |
| **5** | **Phân tích xu hướng & Khoảng trống** | - Thống kê định lượng số lượng công bố theo thời gian.<br>- Xác định các chủ đề đang tăng trưởng nóng và đánh giá tốc độ phát triển của từng nhánh lĩnh vực.<br>- Phát hiện **Research Gap** bằng cách so sánh mật độ công bố giữa các chủ đề để tìm ra vùng dữ liệu tiềm năng ít người khai thác. | - **Không** thực hiện phân tích NLP (Natural Language Processing) trên văn bản toàn văn (full-text) do rào cản bản quyền dữ liệu. |
| **6** | **Trực quan hóa & Dashboard** | - Xây dựng hệ thống đồ thị, biểu đồ trực quan hóa dữ liệu biến động xu hướng theo thời gian.<br>- Cung cấp Dashboard tổng quan hiển thị các thông số đo lường học thuật cho người dùng. | *(Đã bao hàm trọn vẹn trong phạm vi phát triển)* |
| **7** | **Quản lý người dùng & Thư viện** | - Cung cấp không gian lưu trữ cá nhân (Thư viện cá nhân) giúp lưu bài báo, phân loại theo bộ sưu tập.<br>- Tính năng đăng ký theo dõi từ khóa/lĩnh vực và nhận thông báo tự động khi có bài báo mới cập nhật.<br>- Phân hệ Admin quản lý tài khoản, dữ liệu và trạng thái hệ thống. | - **Không** hỗ trợ các tính năng cộng tác nhóm nghiên cứu thời gian thực (như chia sẻ bộ sưu tập dùng chung, bình luận chéo giữa các tài khoản). |
| **8** | **Hỗ trợ AI** | - Sử dụng mô hình ngôn ngữ lớn để tóm tắt bài báo (dựa trên Abstract), gợi ý các tài liệu liên quan, giải thích thuật ngữ chuyên ngành khó và gợi ý hướng nghiên cứu tiếp theo từ abstract. | - **Không** phân tích AI sâu hơn ngoài phạm vi vùng dữ liệu metadata và abstract được phê duyệt hệ thống. |
| **9** | **Tích hợp & Mở rộng** | - Hệ thống hoạt động độc lập, tập trung xử lý dữ liệu học thuật được định nghĩa sẵn. | - **Không** cào hoặc thu thập dữ liệu từ các nguồn phi học thuật (như Blog, Mạng xã hội, Diễn đàn, Tài liệu nội bộ).<br>- **Không** tích hợp trực tiếp với các hệ thống quản lý đào tạo đại học (LMS, SIS) hoặc các công cụ quản lý trích dẫn bên thứ ba (Mendeley, Zotero, EndNote, Overleaf).<br>- **Không** cung cấp API công khai (Public API) cho các bên ngoài khai thác trong giai đoạn này. |
| **10** | **Tính năng nâng cao** | - Phân tích dựa trên số lượng bài báo được xuất bản theo các chiều thời gian và từ khóa. | - **Không** hỗ trợ xây dựng và phân tích mạng lưới trích dẫn sâu (Citation Network, đồng trích dẫn, tính toán thời gian thực chỉ số h-index, Impact Factor). |
| **11** | **Tài chính & Vận hành** | - Vận hành hệ thống quản lý học thuật thuần túy. | - **Không** tích hợp phân hệ quản lý tài chính, phân bổ ngân sách nghiên cứu hay tiến độ đề tài.<br>- **Không** hỗ trợ quy trình nộp bài (Submission) trực tuyến lên các tạp chí hay hội nghị khoa học. |

---

### 6. NGUỒN DỮ LIỆU TÍCH HỢP
Hệ thống tiến hành kết nối API và đồng bộ hóa định kỳ dữ liệu từ các nền tảng học thuật uy tín nhất:
* **OpenAlex:** Kho dữ liệu mở khổng lồ về các công trình nghiên cứu toàn cầu.
* **Semantic Scholar:** Công cụ tìm kiếm học thuật dựa trên trí tuệ nhân tạo của Allen Institute for AI.
* **Crossref:** Cơ quan đăng ký DOI chính thức, cung cấp siêu dữ liệu liên kết cấu trúc lớn.
* **arXiv:** Kho lưu trữ các bản thảo điện tử (preprints) trong các lĩnh vực Toán, Lý, Máy tính...
* **IEEE Xplore:** Thư viện số hàng đầu về Kỹ thuật điện, Điện tử và Khoa học máy tính.
* **ACM Digital Library:** Kho tàng tri thức về lĩnh vực Máy tính và Công nghệ thông tin.

---

### 7. GIÁ TRỊ MANG LẠI CỦA DỰ ÁN
Hệ thống theo dõi xu hướng nghiên cứu khoa học mang lại những giá trị thực tiễn to lớn:
* **Tối ưu hóa thời gian:** Giúp người dùng giảm tới 80% thời gian tìm kiếm thủ công rải rác trên mạng, tập trung nguồn lực vào việc nghiên cứu chuyên môn.
* **Đảm bảo tính mới (Novelty):** Giúp các nhà nghiên cứu tránh được việc chọn trùng các đề tài đã quá bão hòa, đồng thời tìm ra các "mỏ vàng" tri thức thông qua tính năng phát hiện khoảng trống nghiên cứu (Research Gap).
* **Quyết định dựa trên dữ liệu (Data-Driven Decisions):** Giảng viên và sinh viên có cái nhìn toàn cảnh về bức tranh khoa học thế giới, đưa ra quyết định chọn hướng đi dựa trên số liệu thống kê thực tế thay vì cảm tính.
* **Nâng cao chất lượng công bố:** Định hướng người dùng đến những chủ đề mang tính thời sự, từ đó gia tăng khả năng được chấp nhận đăng tải trên các tạp chí quốc tế uy tín.