# Quan điểm Kiểm thử Hệ thống

## Cây Quan điểm Kiểm thử

### STV-L1-001 Chức năng

#### STV-L2-001 Luồng nghiệp vụ quản lý Research Corpus (Thu thập & quản lý dữ liệu bên ngoài)

- **STV-L3-001 Luồng bình thường thu thập & chuẩn hóa dữ liệu tự động** [Cao]
  - Có thể lấy metadata từ nguồn học thuật bên ngoài như OpenAlex, Semantic Scholar, arXiv
  - Dữ liệu thu thập được chuyển đổi và lưu trữ đúng sang schema chung
  - Dữ liệu trùng lặp được xác định và tích hợp đúng theo DOI hoặc tiêu đề
- **STV-L3-002 Quy trình quản lý chất lượng & lưu trữ dữ liệu** [Trung bình]
  - Giá trị bị thiếu và thông tin không nhất quán được phát hiện và loại bỏ đúng cách
  - Điều kiện lưu trữ được áp dụng dựa trên chính sách lưu giữ dữ liệu (※Cần xác nhận)
  - Khi thay đổi cài đặt thêm nguồn dữ liệu, thông tin cấu hình được phản ánh vào hệ thống

#### STV-L2-005 Tìm kiếm & Tra cứu Chi tiết Luận văn (F-003)

- **STV-L3-006 Xác nhận tính năng tìm kiếm luận văn** [Cao]
  - Trong tính năng tìm kiếm từ khóa cơ bản, luận văn được tìm kiếm đúng theo tiêu đề, tóm tắt, từ khóa
  - Tính năng lọc theo thuộc tính (tên tác giả, năm xuất bản, lĩnh vực, nguồn) hoạt động đúng
  - Kết quả truy vấn phức tạp kết hợp điều kiện tìm kiếm nâng cao (AND/OR/NOT) chính xác
  - Tính năng sắp xếp theo mức liên quan, năm xuất bản, số trích dẫn hoạt động bình thường
- **STV-L3-007 Xác nhận hiển thị chi tiết metadata** [Cao]
  - Khi chọn luận văn từ danh sách kết quả, chuyển đúng sang màn hình chi tiết
  - DOI và liên kết đến toàn văn hiển thị đúng ở màn hình chi tiết
  - Metadata và tóm tắt luận văn hiển thị đúng

#### STV-L2-006 Phân tích Xu hướng & Gap (F-004)

- **STV-L3-008 Xác nhận tính năng phân tích xu hướng nghiên cứu** [Cao]
  - Phân tích chuỗi thời gian xu hướng nghiên cứu theo năm/tháng được tổng hợp đúng
  - Tỷ lệ tăng số luận văn được xác định đúng trong tính năng tính tốc độ tăng trưởng lĩnh vực
  - Kết quả phân tích được trực quan hóa đúng dưới dạng biểu đồ trên dashboard

#### STV-L2-008 Luồng nghiệp vụ Engine Phân tích (Phân tích Xu hướng & Gap)

- **STV-L3-010 Xác nhận tính bình thường xử lý phân tích** [Cao]
  - Từ khóa và chủ đề liên quan được trích xuất đúng dựa trên từ khóa tìm kiếm
  - Vùng có ít luận văn được xác định đúng bởi logic phát hiện Research Gap
  - Mối quan hệ trích dẫn và sự chồng chéo nhóm tác giả được phản ánh trong lập bản đồ liên quan

#### STV-L2-009 Liên kết trực quan hóa Dashboard (Trực quan hóa & Dashboard)

- **STV-L3-011 Xác nhận hiển thị component trực quan hóa** [Cao]
  - Biểu đồ chuỗi thời gian xu hướng nghiên cứu hiển thị đúng dựa trên biến động số công bố
  - Heatmap Research Gap cho phép nhận diện vùng có nhiều dư địa nghiên cứu
  - Kết quả phân tích AI (tóm tắt luận văn, chủ đề nghiên cứu đề xuất) được trình bày đúng trên dashboard

#### STV-L2-011 Luồng nghiệp vụ Quản lý Cá nhân hóa (Quản lý Thư viện Cá nhân, Theo dõi Chủ đề)

- **STV-L3-013 Xác nhận luồng bình thường thao tác Thư viện & Collection** [Cao]
  - Có thể lưu luận văn từ kết quả tìm kiếm vào thư viện cá nhân
  - Có thể tạo mới, chỉnh sửa, xóa thư mục và collection
  - Có thể lọc và tìm kiếm tài liệu đã lưu theo từ khóa
- **STV-L3-014 Xác nhận tính năng theo dõi chủ đề và thông báo** [Trung bình]
  - Có thể đặt chủ đề quan tâm hoặc lĩnh vực nghiên cứu làm đối tượng theo dõi
  - Đối tượng theo dõi có thể xem danh sách từ trang cá nhân
  - Thông báo được gửi khi có luận văn mới ※Cần xác nhận: Phương thức thông báo

#### STV-L2-012 Luồng nghiệp vụ Báo cáo Thống kê & Xuất file

- **STV-L3-015 Xác nhận luồng bình thường tính năng báo cáo và xuất** [Trung bình]
  - Số liệu thống kê học thuật dựa trên điều kiện tìm kiếm hiển thị dưới dạng báo cáo tóm tắt
  - Danh sách tài liệu có thể xuất sang định dạng dùng được bởi phần mềm quản lý thư mục
  - Định dạng dữ liệu xuất đúng như kỳ vọng ※Cần xác nhận: Định dạng hỗ trợ

#### STV-L2-015 Luồng nghiệp vụ Hỗ trợ AI (Tóm tắt & Hỗ trợ Luận văn AI)

- **STV-L3-018 Xác nhận hoạt động bình thường tính năng AI** [Cao]
  - Tóm tắt luận văn tự động bằng AI hiển thị đúng ở màn hình chi tiết
  - Khi chọn thuật ngữ chuyên ngành, giải thích hiển thị qua popup hoặc sidebar
  - Tài liệu liên quan đến luận văn đang xem hiển thị dưới dạng gợi ý
  - Liên kết đến dữ liệu luận văn làm cơ sở cho đề xuất và kết quả phân tích AI được đính kèm

#### STV-L2-016 Nghiệp vụ Quản lý Người dùng & Hệ thống

- **STV-L3-019 Xác nhận quản lý tài khoản & quyền hạn** [Cao]
  - Đăng ký và cập nhật hồ sơ người dùng (học sinh, giáo viên, nhà nghiên cứu, quản trị viên) thực hiện bình thường
  - RBAC ngăn người dùng thông thường truy cập màn hình quản lý hệ thống
  - Quản trị viên có thể thay đổi cài đặt hệ thống và thực hiện quản lý vận hành
  - Xử lý đăng nhập và đăng xuất hoàn thành đúng cách

#### STV-L2-018 Quản lý Research Corpus & Liên kết Dữ liệu

- **STV-L3-021 Xác nhận xử lý thu thập & chuẩn hóa dữ liệu bên ngoài** [Cao]
  - Có thể lấy metadata từ API như OpenAlex
  - Dữ liệu thu thập được chuyển đổi đúng sang schema chung
  - Dữ liệu luận văn trùng lặp được tích hợp dựa trên DOI

#### STV-L2-022 Luồng nghiệp vụ Quản lý Thư viện Cá nhân

- **STV-L3-025 Xác nhận thao tác bình thường lưu luận văn & quản lý collection** [Cao]
  - Có thể lưu bình thường vào thư viện cá nhân từ màn hình chi tiết luận văn
  - Có thể tạo mới, đổi tên, xóa collection ở màn hình thư viện cá nhân
  - Có thể di chuyển hoặc xóa luận văn đã lưu giữa các collection

#### STV-L2-023 Quản lý Người dùng & Kiểm soát Quyền hạn

- **STV-L3-026 Xác nhận tính hợp lệ RBAC** [Cao]
  - Chỉ người dùng quản trị mới có thể truy cập dashboard quản lý
  - Người dùng thông thường không thể truy cập thư viện cá nhân của người dùng khác
  - Chỉ người dùng đã xác thực mới có thể sử dụng tính năng lưu luận văn

#### STV-L2-024 Tính năng Trực quan hóa, Phân tích & Thông báo

- **STV-L3-027 Xác nhận tính năng hiển thị dashboard, xuất báo cáo & hỗ trợ AI** [Cao]
  - Biểu đồ chuỗi thời gian xu hướng nghiên cứu và heatmap Research Gap được vẽ đúng
  - Tóm tắt luận văn tự động bằng AI hiển thị phù hợp ở màn hình chi tiết
  - Báo cáo thống kê (CSV/PDF) tải xuống đúng theo định dạng chỉ định
  - Thông báo được kích hoạt khi có luận văn mới cho chủ đề đã theo dõi

#### STV-L2-027 Kiểm tra tính năng Quản lý Người dùng (FR-011)

- **STV-L3-030 Xác nhận luồng xác thực & phân quyền** [Cao]
  - Đăng nhập đúng bằng OAuth2.0/OpenID Connect thành công
  - Kiểm soát quyền theo thuộc tính người dùng dựa trên RBAC hoạt động đúng
  - Khi truy cập trực tiếp API được bảo vệ ở trạng thái chưa xác thực, trả về lỗi 401

#### STV-L2-028 Kiểm tra tính năng Quản lý (FR-012)

- **STV-L3-031 Xác nhận cài đặt quản lý và giám sát** [Trung bình]
  - Cài đặt thêm cơ sở dữ liệu học thuật mới được lưu bình thường
  - Khi thu thập dữ liệu từ API bên ngoài thất bại, hệ thống gửi cảnh báo thông báo cho quản trị viên
  - Log lỗi được ghi lại theo định dạng chỉ định

---

### STV-L1-002 Hiệu suất

#### STV-L2-002 Hiệu suất xử lý dữ liệu lớn

- **STV-L3-003 Xác nhận hoàn thành thực thi xử lý batch** [Cao]
  - Cập nhật tăng dần tất cả nguồn dữ liệu trong batch ban đêm hoàn thành trong 8 giờ
  - Phản hồi xử lý chuyển đổi schema khi có lượng lớn dữ liệu đầu vào trong phạm vi cho phép
  - Các tác vụ thu thập chạy đồng thời không làm cạn kiệt tài nguyên hệ thống

#### STV-L2-007 Hiệu suất phản hồi & dữ liệu lớn (F-003, F-004)

- **STV-L3-009 Tốc độ hiển thị kết quả tìm kiếm và phân tích** [Cao]
  - Kết quả tìm kiếm luận văn hiển thị trong vòng 3 giây
  - Vẽ biểu đồ kết quả phân tích hoàn thành trong vòng 3 giây

#### STV-L2-025 Xác nhận hiệu suất phản hồi

- **STV-L3-028 Xác nhận thời gian phản hồi tìm kiếm và vẽ biểu đồ** [Cao]
  - Hiển thị kết quả tìm kiếm hoàn thành trong vòng 3 giây
  - Vẽ biểu đồ xu hướng nghiên cứu hoàn thành trong vòng 3 giây
  - Màn hình không bị đóng băng khi tải tập dữ liệu lớn

#### STV-L2-033 Hiệu suất phản hồi (Toàn hệ thống)

- **STV-L3-036 Thời gian hiển thị màn hình và xử lý tìm kiếm** [Cao]
  - Hiển thị kết quả tìm kiếm: từ nhập từ khóa đến hiển thị danh sách hoàn thành trong 3 giây
  - Màn hình chi tiết luận văn: hiển thị thông tin luận văn và tóm tắt AI hoàn thành trong 3 giây
  - Màn hình phân tích Research Gap: vẽ heatmap hoàn thành trong 3 giây

#### STV-L2-034 Xử lý dữ liệu lớn (Batch thu thập dữ liệu)

- **STV-L3-037 Xác nhận thời gian hoàn thành xử lý batch** [Trung bình]
  - Cập nhật tăng dần tất cả nguồn dữ liệu trong batch ban đêm hoàn thành trong 8 giờ
  - Xử lý chuẩn hóa dữ liệu không trở thành bottleneck khi nhập lượng lớn luận văn
  - Tài nguyên bộ nhớ không bị tiêu thụ quá mức khi thực thi deduplication

#### STV-L2-035 Thực thi đồng thời & Khả năng chịu tải (Toàn hệ thống)

- **STV-L3-038 Xác nhận hành vi hệ thống khi tải cao** [Cao]
  - Khi nhiều người dùng dùng công cụ tìm kiếm đồng thời, thời gian phản hồi trong phạm vi cho phép
  - Phân tải theo tăng người dùng thực hiện đúng dựa trên thiết kế horizontal scale
  - Kiểm soát độc quyền DB hoạt động đúng và tính nhất quán dữ liệu được duy trì ngay cả khi truy cập đỉnh điểm

---

### STV-L1-003 Sự cố

#### STV-L2-003 Xử lý lỗi liên kết bên ngoài

- **STV-L3-004 Xử lý khi xảy ra sự cố kết nối** [Cao]
  - Xử lý retry thực hiện đúng khi timeout kết nối đến API bên ngoài
  - Cảnh báo thông báo giúp quản trị viên nắm bắt tình trạng lỗi khi thu thập dữ liệu thất bại
  - Xử lý thu thập từ nguồn khác không bị gián đoạn khi một nguồn gặp sự cố

#### STV-L2-030 Kiểm tra khả dụng & phục hồi (FR-001, FR-011)

- **STV-L3-033 Xác nhận backup và phục hồi** [Trung bình]
  - Backup cơ sở dữ liệu được thực hiện đúng hàng ngày
  - Khi sự cố hệ thống, có thể phục hồi trong RPO 24 giờ và RTO 4 giờ
  - Cấu hình dự phòng để đạt khả dụng 99.9% được duy trì

---

### STV-L1-004 Kịch bản Vận hành

#### STV-L2-004 Xuất log & Kiểm toán

- **STV-L3-005 Xác nhận tính nhất quán log vận hành hệ thống** [Trung bình]
  - Số lượng nguồn dữ liệu thu thập và số lượng xuất log khớp nhau
  - Dữ liệu bị loại bỏ bởi quản lý chất lượng (cleaning) được ghi vào log
  - Thời gian bắt đầu/kết thúc và kết quả xử lý batch được ghi lại đúng vào log

#### STV-L2-010 Xử lý dữ liệu & Vận hành tự động hóa (Engine phân tích)

- **STV-L3-012 Xử lý backend và tạo báo cáo** [Trung bình]
  - Định dạng của từng nguồn được tích hợp bởi xử lý chuẩn hóa dữ liệu học thuật
  - Quá trình tự động tạo và cập nhật báo cáo phân tích được thực thi định kỳ
  - Độ chính xác hấp thụ biến thể biểu ký trong phạm vi cho phép bởi engine phân tích (※Cần xác nhận)

---

### STV-L1-011 Bảo mật

#### STV-L2-014 Quản lý quyền & Bảo vệ dữ liệu

- **STV-L3-017 Xác nhận phân quyền dữ liệu cá nhân** [Cao]
  - Không thể truy cập thư viện cá nhân và luận văn đã lưu của người dùng khác qua URL trực tiếp
  - Thông tin tài liệu đã lưu không bị xem bởi người dùng khác
  - Thao tác bị từ chối khi token xác thực đã hết hạn

#### STV-L2-017 Xác thực & Phân quyền (Tính năng Quản lý Người dùng)

- **STV-L3-020 Xác nhận giới hạn truy cập quyền hạn** [Cao]
  - Bị từ chối khi truy cập chức năng quản lý qua URL trực tiếp không có thông tin xác thực
  - Thực thi API ngoài quyền hạn bị cấm dựa trên cài đặt RBAC
  - Masking thông tin cá nhân và nội dung nghiên cứu chưa công bố được áp dụng cho dữ liệu gửi đến AI

#### STV-L2-026 Xác thực & Bảo vệ dữ liệu

- **STV-L3-029 Xác thực an toàn & bảo vệ khi sử dụng AI** [Cao]
  - Đăng nhập bằng OAuth2.0/OpenID Connect được xử lý đúng
  - Khi gửi query đến mô hình AI, thông tin cá nhân được masking
  - Bị từ chối khi thử truy cập trực tiếp dashboard quản lý ở trạng thái chưa đăng nhập

#### STV-L2-031 Kiểm tra tính năng truyền thông & bảo vệ (FR-011, FR-012)

- **STV-L3-034 Mã hóa truyền thông & bảo vệ dữ liệu** [Cao]
  - TLS 1.3 trở lên được áp dụng cho tất cả truyền thông nội ngoại
  - Xử lý masking thông tin cá nhân người dùng thực thi khi gửi dữ liệu đến mô hình AI
  - Token xác thực API được quản lý đúng cách và rủi ro rò rỉ được ngăn chặn

#### STV-L2-032 Bảo vệ thông tin cá nhân khi sử dụng AI (NFR-010)

- **STV-L3-035 Xác nhận masking khi gửi query đến mô hình AI** [Cao]
  - Trong tính năng tóm tắt luận văn, xác nhận dữ liệu query gửi đi không chứa thông tin cá nhân (họ tên, email)
  - Trước khi gửi nội dung nghiên cứu chưa công bố hoặc thông tin chỉ người dùng cụ thể mới xem được đến mô hình AI, xử lý masking thực thi đúng
  - Kiểm tra nội dung truyền thông request gửi đến AI và xác nhận thông tin cá nhân và bí mật được bảo vệ

#### STV-L2-036 Xác thực & Phân quyền (Toàn hệ thống)

- **STV-L3-039 Xác nhận tính hợp lệ chức năng xác thực** [Cao]
  - Xử lý đăng nhập bằng OAuth2.0/OpenID Connect hoàn thành bình thường
  - Session kết thúc đúng cách khi token xác thực hết hạn
  - Session vô hiệu hóa bởi thao tác đăng xuất và không thể truy cập lại
- **STV-L3-040 Xác nhận RBAC (Kiểm soát Truy cập Dựa trên Vai trò)** [Cao]
  - Chỉ quyền quản trị mới có thể truy cập "Dashboard Quản lý"
  - Khi người dùng thông thường gọi trực tiếp API chức năng quản lý, xảy ra lỗi quyền hạn
  - Khả năng sử dụng từng chức năng được giới hạn đúng dựa trên cài đặt phân quyền

#### STV-L2-037 Xác thực giá trị đầu vào (Nhập liệu tìm kiếm & Quản lý Thư viện)

- **STV-L3-041 Tránh rủi ro bảo mật do đầu vào bất hợp pháp** [Cao]
  - Chuỗi tấn công SQL injection vào hộp tìm kiếm được xử lý đúng cách
  - Chuỗi tấn công XSS (script tag, v.v.) vào tên collection được vô hiệu hóa khi hiển thị
  - Validation phía server được thực hiện với giá trị đầu vào và dữ liệu bất hợp pháp không được đăng ký vào DB

#### STV-L2-038 Security Header (Tất cả màn hình hệ thống)

- **STV-L3-042 Biện pháp bảo mật truyền thông & trình duyệt** [Cao]
  - TLS 1.3 trở lên được bắt buộc trong tất cả HTTP request
  - Security header phù hợp (HSTS, Content-Security-Policy, v.v.) được bao gồm trong response
  - Thông tin cá nhân người dùng được masking khi sử dụng AI và không gửi đến model

---

## Ma trận Phủ sóng

| Đối tượng Kiểm thử | Chức năng | Hiệu suất | Sự cố | Kịch bản Vận hành | Bảo mật |
| --- | --- | --- | --- | --- | --- |
| Thu thập & Quản lý Dữ liệu Bên ngoài | ○ | ○ | ○ | ○ | - |
| Quản lý Dữ liệu Luận văn | ○ | ○ | ○ | ○ | - |
| Tìm kiếm & Tra cứu Chi tiết Luận văn | ○ | ○ | - | - | - |
| Phân tích Xu hướng & Gap | ○ | ○ | - | - | - |
| Dashboard Trực quan hóa | ○ | - | - | - | - |
| Xuất Báo cáo Thống kê | ○ | ○ | - | - | - |
| Lưu Tài liệu | ○ | ○ | - | - | ○ |
| Quản lý Collection | ○ | ○ | - | - | ○ |
| Theo dõi Chủ đề Quan tâm | ○ | - | - | - | ○ |
| Tính năng Thông báo Cập nhật | ○ | - | - | - | - |
| Tìm kiếm trong Thư viện | ○ | ○ | - | - | ○ |
| Tính năng Hỗ trợ AI | ○ | - | - | - | ○ |
| Tính năng Quản lý Người dùng | ○ | - | - | - | ○ |
| Dashboard Quản trị | ○ | - | ○ | - | ○ |
| Nền tảng Hệ thống | - | ○ | ○ | - | ○ |
| Xác thực & Phân quyền | - | - | - | - | ○ |
| Nhập liệu tìm kiếm & Quản lý Thư viện | - | - | - | - | ○ |
| Truyền thông & Header | - | - | - | - | ○ |

Chú thích: ○=Cần kiểm thử, △=Có điều kiện, -=Không áp dụng
