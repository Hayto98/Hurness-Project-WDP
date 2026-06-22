# Danh sách GitHub Issue

```json
{
  "summary": {
    "totalIssues": 12,
    "byType": {
      "feature": 4,
      "ui": 3,
      "api": 3,
      "database": 1,
      "test": 1
    },
    "byPriority": {
      "high": 9,
      "medium": 3,
      "low": 0
    }
  },
  "labels": [
    {
      "name": "type:feature",
      "color": "0E8A16",
      "description": "Tính năng mới"
    },
    {
      "name": "type:ui",
      "color": "1D76DB",
      "description": "Triển khai màn hình"
    },
    {
      "name": "type:api",
      "color": "D93F0B",
      "description": "Triển khai API"
    },
    {
      "name": "type:database",
      "color": "BFD4F2",
      "description": "DB/Migration"
    },
    {
      "name": "type:test",
      "color": "FBCA04",
      "description": "Kiểm thử"
    },
    {
      "name": "priority:high",
      "color": "B60205",
      "description": "Ưu tiên cao"
    },
    {
      "name": "priority:medium",
      "color": "FBCA04",
      "description": "Ưu tiên trung bình"
    },
    {
      "name": "priority:low",
      "color": "0E8A16",
      "description": "Ưu tiên thấp"
    },
    {
      "name": "gear-generated",
      "color": "CCCCCC",
      "description": "Tự động tạo bởi GEAR"
    }
  ],
  "issues": [
    {
      "id": "ISSUE-001",
      "title": "[Database] Xây dựng Schema",
      "body": "## Tổng quan\nTriển khai migration thiết kế bảng nền tảng hệ thống (users, research_papers, user_libraries, v.v.).\n\n## Điều kiện chấp nhận\n- [ ] Tạo bảng users, research_papers, user_libraries\n- [ ] Tạo bảng topic_subscriptions, analysis_logs\n- [ ] Xác nhận tính hợp lệ của ràng buộc khóa ngoại\n\n## Thiết kế liên quan\n- Toàn bộ định nghĩa bảng\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:database",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Định nghĩa bảng",
        "itemId": "ALL"
      },
      "dependencies": {
        "blockedBy": []
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-002",
      "title": "[Feature] Tính năng Xác thực & Quản lý Người dùng",
      "body": "## Tổng quan\nTriển khai tính năng đăng nhập người dùng và quản lý hồ sơ.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai /api/auth/login\n- [ ] Cấp phát access token bằng JWT, v.v.\n- [ ] Triển khai nền tảng kiểm soát truy cập theo vai trò người dùng\n\n## Thiết kế liên quan\n- API: POST /api/auth/login\n- Bảng: users\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:feature",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách yêu cầu chức năng",
        "itemId": "FR-011"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-001"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-003",
      "title": "[Feature] Thu thập & Chuẩn hóa Dữ liệu Luận văn",
      "body": "## Tổng quan\nPipeline lấy luận văn từ nguồn bên ngoài như OpenAlex, chuẩn hóa và lưu vào DB.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai xử lý batch tích hợp API bên ngoài\n- [ ] Triển khai logic loại bỏ trùng lặp theo DOI\n- [ ] Triển khai API kích hoạt batch quản lý\n\n## Thiết kế liên quan\n- API: POST /api/admin/sources\n- Chức năng: FR-001, FR-002\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:feature",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách yêu cầu chức năng",
        "itemId": "FR-001, FR-002"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-001"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-004",
      "title": "[Feature] Công cụ Tìm kiếm & Hiển thị Chi tiết",
      "body": "## Tổng quan\nTìm kiếm luận văn, lấy danh sách và cung cấp thông tin chi tiết.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai /api/papers (GET) (tìm kiếm, lọc)\n- [ ] Triển khai /api/papers/{id} (GET)\n\n## Thiết kế liên quan\n- API: /api/papers, /api/papers/{id}\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:feature",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách yêu cầu chức năng",
        "itemId": "FR-003, FR-004"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-003"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-005",
      "title": "[UI] Màn hình Tìm kiếm & Chi tiết Luận văn",
      "body": "## Tổng quan\nTriển khai màn hình hiển thị kết quả tìm kiếm và chi tiết luận văn.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai SCR-002 (Danh sách kết quả tìm kiếm)\n- [ ] Triển khai SCR-003 (Chi tiết luận văn)\n- [ ] Triển khai phần hiển thị tóm tắt AI (※Cần xác nhận: Điểm kết nối LLM)\n\n## Thiết kế liên quan\n- Đặc tả màn hình: SCR-002, SCR-003\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:ui",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách màn hình",
        "itemId": "SCR-002, SCR-003"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-004"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-006",
      "title": "[Feature] Tính năng Phân tích AI & Phân tích Xu hướng",
      "body": "## Tổng quan\nTriển khai phân tích thống kê luận văn và tính năng tóm tắt AI.\n\n## Điều kiện chấp nhận\n- [ ] Xử lý phân tích xu hướng (/api/analysis/trends)\n- [ ] Lấy tóm tắt AI (/api/papers/{id}/summary)\n\n## Thiết kế liên quan\n- API: /api/analysis/trends, /api/papers/{id}/summary\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:feature",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách yêu cầu chức năng",
        "itemId": "FR-005, FR-009"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-004"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-007",
      "title": "[UI] Dashboard & Màn hình Trực quan hóa",
      "body": "## Tổng quan\nTriển khai màn hình trực quan hóa xu hướng và phân tích Research Gap.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai SCR-001 (Dashboard chính)\n- [ ] Triển khai SCR-004 (Phân tích Research Gap)\n- [ ] Tích hợp thư viện vẽ biểu đồ\n\n## Thiết kế liên quan\n- Đặc tả màn hình: SCR-001, SCR-004\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:ui",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách màn hình",
        "itemId": "SCR-001, SCR-004"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-006"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-008",
      "title": "[Feature] Quản lý Thư viện Cá nhân",
      "body": "## Tổng quan\nTính năng lưu luận văn và quản lý collection.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai API lưu (/api/libraries/papers)\n- [ ] Triển khai tính năng CRUD màn hình thư viện\n\n## Thiết kế liên quan\n- API: /api/libraries/papers\n- Màn hình: SCR-005\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:feature",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách yêu cầu chức năng",
        "itemId": "FR-008"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-002",
          "ISSUE-005"
        ]
      },
      "priority": "high"
    },
    {
      "id": "ISSUE-009",
      "title": "[API] API Dashboard Quản trị",
      "body": "## Tổng quan\nTriển khai endpoint giám sát hệ thống và quản lý log.\n\n## Điều kiện chấp nhận\n- [ ] API xem log quản lý\n- [ ] Logic kiểm tra quyền màn hình quản lý\n\n## Thiết kế liên quan\n- FR-012, SCR-008\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:api",
        "priority:medium",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Đặc tả API",
        "itemId": "FR-012"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-001"
        ]
      },
      "priority": "medium"
    },
    {
      "id": "ISSUE-010",
      "title": "[Feature] Thông báo Chủ đề Đã theo dõi",
      "body": "## Tổng quan\nTriển khai tính năng theo dõi chủ đề và thông báo.\n\n## Điều kiện chấp nhận\n- [ ] Logic đăng ký chủ đề\n- [ ] Xử lý trigger thông báo (※Cần xác nhận: Kênh thông báo)\n\n## Thiết kế liên quan\n- FR-010, SCR-006\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:feature",
        "priority:medium",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách yêu cầu chức năng",
        "itemId": "FR-010"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-002"
        ]
      },
      "priority": "medium"
    },
    {
      "id": "ISSUE-011",
      "title": "[UI] Các Màn hình Cài đặt Khác",
      "body": "## Tổng quan\nTriển khai màn hình cài đặt thông báo và màn hình quản lý.\n\n## Điều kiện chấp nhận\n- [ ] Triển khai SCR-006, SCR-008\n\n## Thiết kế liên quan\n- Đặc tả màn hình: SCR-006, SCR-008\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:ui",
        "priority:medium",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Danh sách màn hình",
        "itemId": "SCR-006, SCR-008"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-009",
          "ISSUE-010"
        ]
      },
      "priority": "medium"
    },
    {
      "id": "ISSUE-012",
      "title": "[Test] Kiểm thử Tích hợp Tổng thể",
      "body": "## Tổng quan\nThực hiện kiểm thử tích hợp API và DB.\n\n## Điều kiện chấp nhận\n- [ ] Kiểm thử xác thực (IT-001)\n- [ ] Kiểm thử phân quyền (IT-002)\n- [ ] Kiểm thử nhất quán & giao dịch (IT-003, IT-004, IT-005)\n\n## Thiết kế liên quan\n- Đặc tả kiểm thử tích hợp\n\n---\n*Được tạo bởi GEAR.indigo*",
      "labels": [
        "type:test",
        "priority:high",
        "gear-generated"
      ],
      "sourceArtifact": {
        "type": "Kiểm thử tích hợp",
        "itemId": "ALL"
      },
      "dependencies": {
        "blockedBy": [
          "ISSUE-002",
          "ISSUE-003",
          "ISSUE-004",
          "ISSUE-006"
        ]
      },
      "priority": "high"
    }
  ]
}
```
