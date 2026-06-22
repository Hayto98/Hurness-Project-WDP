# Tài liệu Định nghĩa UI Màn hình

```json
{
  "screens": [
    {
      "screenId": "SCR-001",
      "screenName": "Trang chủ (Dashboard)",
      "category": "Dashboard",
      "targetUser": "Học sinh, giáo viên, nhà nghiên cứu",
      "overview": "Màn hình chính hiển thị tổng quan biểu đồ trực quan hóa xu hướng nghiên cứu và kết quả phân tích chính, cung cấp điểm vào đến lĩnh vực quan tâm. ※Cần xác nhận: Cấu trúc layout",
      "components": [
        {
          "name": "Header",
          "type": "header",
          "description": "Menu điều hướng"
        },
        {
          "name": "Trend Chart",
          "type": "card",
          "description": "Biểu đồ biến động xu hướng nghiên cứu theo chuỗi thời gian"
        },
        {
          "name": "Research Gap Summary",
          "type": "card",
          "description": "Liên kết đến Research Gap nổi bật và heatmap thu gọn"
        },
        {
          "name": "Search Box",
          "type": "search-box",
          "description": "Trường nhập tìm kiếm chính"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "Nhập từ khóa tìm kiếm hoặc nhấp vào biểu đồ xu hướng",
          "systemResponse": "Chuyển đến màn hình kết quả tìm kiếm hoặc chi tiết phân tích tương ứng"
        }
      ],
      "fields": [
        {
          "name": "mainSearch",
          "type": "text",
          "required": false,
          "validation": "Không có",
          "description": "Hộp văn bản tìm kiếm luận văn nghiên cứu"
        }
      ],
      "events": [
        {
          "trigger": "Nhấp nút tìm kiếm",
          "action": "Chuyển đến màn hình Tìm kiếm & Kết quả Luận văn",
          "description": "Truyền từ khóa đã nhập làm query"
        }
      ],
      "transitions": [
        {
          "action": "Thực thi tìm kiếm",
          "destination": "SCR-002",
          "condition": "Có nhập từ khóa"
        },
        {
          "action": "Nhấp card Gap",
          "destination": "SCR-004",
          "condition": "Không có"
        }
      ]
    },
    {
      "screenId": "SCR-002",
      "screenName": "Màn hình Tìm kiếm & Kết quả Luận văn",
      "category": "Giao dịch",
      "targetUser": "Học sinh, giáo viên, nhà nghiên cứu",
      "overview": "Màn hình tìm kiếm luận văn theo từ khóa hoặc thuộc tính và hiển thị kết quả tìm kiếm dưới dạng danh sách. Cung cấp tính năng lọc.",
      "components": [
        {
          "name": "Search Controls",
          "type": "search-box",
          "description": "Bộ lọc điều kiện tìm kiếm nâng cao (năm, tác giả, lĩnh vực, v.v.)"
        },
        {
          "name": "Results Table",
          "type": "table",
          "description": "Hiển thị danh sách kết quả tìm kiếm (tiêu đề luận văn, tác giả, năm)"
        },
        {
          "name": "Pagination",
          "type": "pagination",
          "description": "Phân trang kết quả tìm kiếm"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "Nhập điều kiện tìm kiếm và tìm kiếm",
          "systemResponse": "Hiển thị danh sách luận văn phù hợp điều kiện"
        },
        {
          "step": 2,
          "action": "Chọn luận văn cụ thể",
          "systemResponse": "Chuyển đến màn hình chi tiết luận văn"
        }
      ],
      "fields": [
        {
          "name": "keyword",
          "type": "text",
          "required": false,
          "validation": "Không có",
          "description": "Tìm kiếm từ khóa"
        },
        {
          "name": "yearRange",
          "type": "date",
          "required": false,
          "validation": "Năm bắt đầu <= Năm kết thúc",
          "description": "Lọc theo năm xuất bản"
        }
      ],
      "events": [
        {
          "trigger": "Nhấp vào hàng",
          "action": "Chuyển đến màn hình chi tiết",
          "description": "Truyền ID luận văn được chọn làm tham số URL"
        }
      ],
      "transitions": [
        {
          "action": "Chọn hàng",
          "destination": "SCR-003",
          "condition": "Không có"
        }
      ]
    },
    {
      "screenId": "SCR-003",
      "screenName": "Màn hình Chi tiết Luận văn",
      "category": "Giao dịch",
      "targetUser": "Học sinh, giáo viên, nhà nghiên cứu",
      "overview": "Màn hình hiển thị metadata và tóm tắt luận văn, cung cấp tóm tắt AI và liên kết đến luận văn liên quan.",
      "components": [
        {
          "name": "Paper Info",
          "type": "card",
          "description": "Thông tin cơ bản của luận văn (tiêu đề, tác giả, tóm tắt)"
        },
        {
          "name": "AI Summary",
          "type": "card",
          "description": "Phần tóm tắt AI"
        },
        {
          "name": "Related Papers",
          "type": "table",
          "description": "Danh sách luận văn liên quan"
        },
        {
          "name": "Library Action",
          "type": "button-group",
          "description": "Nút lưu vào thư viện cá nhân"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "Nhấn nút tóm tắt AI",
          "systemResponse": "Hiển thị tóm tắt luận văn bằng AI"
        },
        {
          "step": 2,
          "action": "Nhấn nút lưu",
          "systemResponse": "Thêm vào thư viện cá nhân"
        }
      ],
      "fields": [],
      "events": [
        {
          "trigger": "Nhấp nút lưu",
          "action": "Giao tiếp API",
          "description": "Gửi yêu cầu thêm vào thư viện"
        }
      ],
      "transitions": [
        {
          "action": "Nút quay lại",
          "destination": "SCR-002",
          "condition": "Không có"
        }
      ]
    },
    {
      "screenId": "SCR-004",
      "screenName": "Màn hình Phân tích Research Gap",
      "category": "Báo cáo",
      "targetUser": "Nhà nghiên cứu, giáo viên",
      "overview": "Màn hình phân tích trực quan xác định vùng trống nghiên cứu trong lĩnh vực cụ thể bằng heatmap, v.v.",
      "components": [
        {
          "name": "Heatmap View",
          "type": "card",
          "description": "Heatmap tương quan giữa lĩnh vực nghiên cứu và số lượng nghiên cứu"
        },
        {
          "name": "Filter Tabs",
          "type": "tabs",
          "description": "Chuyển đổi lĩnh vực nghiên cứu mục tiêu"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "Chọn lĩnh vực qua tab",
          "systemResponse": "Vẽ heatmap của lĩnh vực được chọn"
        }
      ],
      "fields": [
        {
          "name": "fieldSelection",
          "type": "select",
          "required": true,
          "validation": "Chưa xác định",
          "description": "Chọn lĩnh vực chuyên môn cần phân tích"
        }
      ],
      "events": [
        {
          "trigger": "Nhấp vùng heatmap",
          "action": "Drill-down chi tiết",
          "description": "Đến danh sách luận văn của vùng tương ứng"
        }
      ],
      "transitions": [
        {
          "action": "Chuyển đến tìm kiếm",
          "destination": "SCR-002",
          "condition": "Không có"
        }
      ]
    },
    {
      "screenId": "SCR-005",
      "screenName": "Thư viện Cá nhân",
      "category": "Giao dịch",
      "targetUser": "Học sinh, giáo viên, nhà nghiên cứu",
      "overview": "Trang riêng của người dùng để xem và quản lý luận văn đã lưu và collection đã tạo.",
      "components": [
        {
          "name": "Collection List",
          "type": "sidebar",
          "description": "Danh sách thư mục lưu trữ"
        },
        {
          "name": "Saved Papers",
          "type": "table",
          "description": "Danh sách luận văn đã lưu"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "Chọn collection",
          "systemResponse": "Hiển thị luận văn trong collection được chọn"
        },
        {
          "step": 2,
          "action": "Nhấn nút xóa luận văn",
          "systemResponse": "Xóa khỏi danh sách"
        }
      ],
      "fields": [
        {
          "name": "collectionName",
          "type": "text",
          "required": true,
          "validation": "Không được để trống",
          "description": "Tên thư mục mới tạo"
        }
      ],
      "events": [
        {
          "trigger": "Nhấp nút xóa",
          "action": "Hiển thị modal xác nhận xóa",
          "description": "※Cần xác nhận: Luồng xóa"
        }
      ],
      "transitions": [
        {
          "action": "Nhấp tiêu đề",
          "destination": "SCR-003",
          "condition": "Không có"
        }
      ]
    }
  ]
}
```
