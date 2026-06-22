# Hurness-Project-WDP

> Hệ thống Theo dõi Xu hướng Nghiên cứu Khoa học (Scientific Research Trend Tracking System)

## 📚 Thứ tự đọc tài liệu khuyến nghị

### 🟦 Giai đoạn 1 — Hiểu dự án (Bức tranh toàn cảnh)
1. [project-overview.md](docs/VN/project-overview.md) — Mục đích, bối cảnh, phạm vi dự án
2. [proposal-slides.md](docs/VN/proposal-slides.md) — Tóm tắt nhanh toàn bộ dự án (như slide thuyết trình)

### 🟩 Giai đoạn 2 — Hiểu Yêu cầu
3. [business-requirements.md](docs/VN/business-requirements.md) — Yêu cầu nghiệp vụ (BR-001 ~ BR-042)
4. [functional-requirements.md](docs/VN/functional-requirements.md) — Yêu cầu chức năng (FR-001 ~ FR-013)
5. [non-functional-requirements.md](docs/VN/non-functional-requirements.md) — Yêu cầu phi chức năng (hiệu suất, bảo mật...)

### 🟧 Giai đoạn 3 — Hiểu Kiến trúc & Thiết kế Hệ thống
6. [system-overview.md](docs/VN/system-overview.md) — Cấu trúc tổng thể hệ thống, stack công nghệ
7. [architecture-diagram.md](docs/VN/architecture-diagram.md) — Sơ đồ kiến trúc (client → gateway → app → data)
8. [er-diagram.md](docs/VN/er-diagram.md) — Sơ đồ quan hệ entity
9. [table-definitions.md](docs/VN/table-definitions.md) — Định nghĩa chi tiết các bảng DB

### 🟨 Giai đoạn 4 — Hiểu Luồng & Màn hình
10. [business-flow.md](docs/VN/business-flow.md) — Luồng nghiệp vụ (thu thập dữ liệu, phân tích, AI)
11. [sequence-diagram.md](docs/VN/sequence-diagram.md) — Sơ đồ trình tự (xác thực, tìm kiếm, phân tích, cập nhật)
12. [screen-list.md](docs/VN/screen-list.md) — Danh sách màn hình (SCR-001 ~ SCR-008)
13. [screen-transition.md](docs/VN/screen-transition.md) — Luồng chuyển đổi giữa các màn hình
14. [screen-ui-spec.md](docs/VN/screen-ui-spec.md) — Đặc tả UI chi tiết từng màn hình

### 🟥 Giai đoạn 5 — Hiểu Triển khai
15. [backend-process.md](docs/VN/backend-process.md) — Các quy trình xử lý backend (BP-001 ~ BP-006)
16. [api-specification.md](docs/VN/api-specification.md) — Đặc tả API endpoint
17. [implementation-rules.md](docs/VN/implementation-rules.md) — Quy tắc coding và cấu trúc tài liệu

### ⬛ Giai đoạn 6 — Kiểm thử
18. [test-plan-map.md](docs/VN/test-plan-map.md) — Bản đồ kế hoạch kiểm thử tổng thể
19. [integration-test.md](docs/VN/integration-test.md) — Test case kiểm thử tích hợp (IT-001 ~ IT-005)
20. [integration-test-viewpoints.md](docs/VN/integration-test-viewpoints.md) — Quan điểm kiểm thử tích hợp chi tiết
21. [system-test.md](docs/VN/system-test.md) — Test case kiểm thử hệ thống (ST-001 ~ ST-002)
22. [system-test-viewpoints.md](docs/VN/system-test-viewpoints.md) — Quan điểm kiểm thử hệ thống chi tiết
23. [github-issues.md](docs/VN/github-issues.md) — Danh sách issues và thứ tự ưu tiên triển khai

---

> 💡 **Mẹo**: Nếu chỉ có thời gian hạn chế, đọc **1 → 4 → 7 → 16 → 23** là đủ để hiểu toàn bộ dự án và bắt đầu code.