---
title: "Worklog Tuần 5"
date: 2026-10-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---


### Mục tiêu tuần 5:

* Hiểu rõ mục đích và ranh giới của Encrypted Media Extensions (EME), bao gồm vai trò của trình duyệt, Content Decryption Module (CDM), hệ thống khóa (key system), ứng dụng và máy chủ cấp phép/khóa (license/key server).
* Tìm hiểu cách Shaka Player kết nối một manifest được mã hóa với hệ thống khóa và endpoint cấp phép đã cấu hình, đồng thời xác định các thiết lập cần thiết cho môi trường phát lại hợp lệ.
* Hiểu cách Shaka Packager mã hóa nội dung truyền thông và báo hiệu thông tin bảo vệ trong các đầu ra DASH/HLS, từ đó phác thảo một quy trình tích hợp DRM nhỏ có thể kiểm thử cho SecureLearn.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 1 | - Nghiên cứu tổng quan về EME và mục đích của nó.<br>- Phân biệt EME (một API của trình duyệt) với một hệ thống DRM hoàn chỉnh hoặc máy chủ cấp phép.<br>- Xác định vai trò của ứng dụng web, trình duyệt/CDM, hệ thống khóa, phương tiện mã hóa và dịch vụ cấp phép.<br>- Lưu ý rằng xác thực và ủy quyền ở cấp ứng dụng không được cung cấp tự động bởi EME.<br>- Đầu ra: luồng phát lại DRM cấp cao và bảng thuật ngữ. | 12/10/2026 | 12/10/2026 | - W3C: Encrypted Media Extensions<br>- Shaka Player: DRM Configuration |
| 2 | - Theo dõi vòng đời EME: requestMediaKeySystemAccess(), MediaKeys và MediaKeySession.<br>- Hiểu cách dữ liệu khởi tạo kích hoạt generateRequest() và sự kiện message.<br>- Lập bản đồ trao đổi giấy phép: ứng dụng gửi thông báo tới dịch vụ, nhận phản hồi, sau đó chuyển nó đến session.update().<br>- Xem lại các sự kiện trạng thái khóa và sự khác biệt giữa phiên giấy phép tạm thời và vĩnh viễn.<br>- Đầu ra: sơ đồ trình tự trao đổi giữa trình duyệt và dịch vụ cấp phép. | 13/10/2026 | 13/10/2026 | - W3C: Encrypted Media Extensions |
| 3 | - Nghiên cứu player.configure() và ánh xạ drm.servers giữa ID hệ thống khóa và URL máy chủ cấp phép.<br>- Xem lại cấu hình Clear Key để kiểm tra và chẩn đoán có kiểm soát; hiểu rằng nó không tương đương với DRM thương mại cấp sản xuất.<br>- Kiểm tra các tùy chọn drm.advanced như tiêu đề yêu cầu cấp phép và các yêu cầu về độ bền của hệ thống khóa.<br>- Kiểm tra các yêu cầu về môi trường: sử dụng HTTPS hoặc localhost cho EME, đồng thời giữ manifest và các phân đoạn trên HTTPS khi trang sử dụng HTTPS.<br>- Đầu ra: danh sách kiểm tra cấu hình DRM của Shaka Player. | 14/10/2026 | 14/10/2026 | - Shaka Player: DRM Configuration<br>- W3C: Encrypted Media Extensions |
| 4 | - Nghiên cứu các tùy chọn nguồn khóa của Shaka Packager: khóa thô (raw keys), máy chủ khóa được hỗ trợ và CPIX.<br>- Hiểu ID khóa (KID), khóa nội dung, IV và báo hiệu PSSH/bảo vệ, cách bộ đóng gói ghi thông tin bảo vệ vào manifest đầu ra.<br>- So sánh các sơ đồ bảo vệ được hỗ trợ như cenc và cbcs, xem xét tùy chọn protection_systems.<br>- Xem xét các cài đặt clear-lead và xoay vòng khóa; giữ khóa thô và IV thử nghiệm giới hạn trong môi trường thử nghiệm được kiểm soát.<br>- Đầu ra: danh sách kiểm tra thông số đóng gói và phương pháp khóa kiểm tra được tài liệu hóa. | 15/10/2026 | 15/10/2026 | - Shaka Packager: DRM |
| 5 | - Kết hợp các khái niệm thành một luồng: phương tiện nguồn -> Shaka Packager -> các phân đoạn và manifest được mã hóa -> Shaka Player/EME -> phản hồi khóa hoặc giấy phép -> phát lại.<br>- Xác định các kiểm tra cơ bản: phát lại rõ ràng, phát lại được bảo vệ với khóa kiểm tra khớp, sai khóa/KID, endpoint không chính xác, hệ thống khóa không được hỗ trợ và URL không bảo mật/nội dung hỗn hợp.<br>- Tách biệt bản demo Clear Key cục bộ khỏi việc triển khai Widevine/PlayReady thực tế, đòi hỏi cơ sở hạ tầng hệ thống khóa/giấy phép tương thích và quyền truy cập thích hợp.<br>- Củng cố ghi chú, xác định các phụ thuộc tích hợp và ghi lại các câu hỏi mở cho giai đoạn phát triển tiếp theo.<br>- Đầu ra: sơ đồ đầu cuối và ma trận kiểm tra nhỏ. | 16/10/2026 | 16/10/2026 | - W3C: Encrypted Media Extensions<br>- Shaka Player: DRM Configuration<br>- Shaka Packager: DRM |

### Kết quả đạt được tuần 5:

* Giải thích vai trò của EME như một giao diện giữa ứng dụng web và các hệ thống khóa trình duyệt được hỗ trợ, thay vì là một triển khai DRM độc lập hoặc máy chủ cấp phép.
* Tài liệu hóa vòng đời yêu cầu giấy phép chính: lựa chọn hệ thống khóa, tạo phiên, chuyển tin nhắn đến dịch vụ, xử lý phản hồi và giám sát trạng thái khóa.
* Xác định các cài đặt cốt lõi của Shaka Player để phát lại được bảo vệ, bao gồm drm.servers, cấu hình nâng cao tùy chọn và các điều kiện yêu cầu bởi ngữ cảnh trình duyệt bảo mật.
* Tóm tắt các trách nhiệm và khái niệm chính của Shaka Packager, bao gồm đầu vào khóa thô/máy chủ khóa/CPIX, liên kết KID và khóa nội dung, sơ đồ bảo vệ, báo hiệu manifest và xoay vòng khóa.
* Chuẩn bị phác thảo tích hợp thực tế cho bản demo có kiểm soát, trong đó việc kiểm tra Clear Key/khóa thô được tách biệt khỏi các kỳ vọng và thông tin xác thực DRM sản xuất.
* Tạo danh sách kiểm tra thử nghiệm được đề xuất bao gồm phát lại thành công, không khớp khóa/manifest, lỗi endpoint cấp phép, hỗ trợ trình duyệt và các yêu cầu URL bảo mật.

