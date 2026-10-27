---
title: "Blog 1"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# AWS Security: Zero Trust & Least Privilege – Khi "Không tin ai" là cách bảo vệ hệ thống tốt nhất

Trong nhiều hệ thống mạng truyền thống, bảo mật thường được thiết lập dựa trên ranh giới mạng (network boundary), nghĩa là nếu bạn ở trong mạng nội bộ, bạn mặc định được hệ thống tin tưởng. Cách tiếp cận này khá đơn giản nhưng không còn đủ an toàn trong môi trường cloud hiện đại. Chỉ cần một kẻ tấn công vượt qua được lớp rào chắn bên ngoài, hệ thống có thể bị xâm nhập toàn diện do không có lớp bảo vệ khắt khe bên trong.
Trong quá trình tìm hiểu AWS, mình đọc được các tài liệu khá thú vị về việc áp dụng kiến trúc Zero Trust (Không tin cậy) và nguyên tắc Least Privilege (Đặc quyền tối thiểu). Các bài viết này (trong đó có bài từ AWS Builder Center vào tháng 9/2026) phân tích sâu về cách dịch chuyển từ mô hình tin tưởng mạng sang tin tưởng danh tính. Điều mình thấy thú vị là bài toán này không dùng một dịch vụ duy nhất, mà kết hợp nhiều dịch vụ AWS thành một hệ thống bảo vệ nhiều lớp hoàn chỉnh.
### BÀI TOÁN CẦN GIẢI QUYẾT
Hãy thử tưởng tượng một hệ thống đám mây nơi các dịch vụ và người dùng liên tục tương tác với nhau:
* Người dùng truy cập vào ứng dụng nội bộ.
* Microservices kết nối với cơ sở dữ liệu.
* Ứng dụng bên thứ ba gọi API nội bộ.
Các thiết bị quản trị kết nối vào server.
Nếu chỉ dựa vào vị trí mạng như: "Cứ request đến từ Private Subnet là mặc định an toàn", thì hệ thống có thể đối mặt với rủi ro cực lớn. Lúc này, việc cấp quyền dựa trên network location (vị trí mạng) sẽ không còn đáng tin cậy.
Giải pháp được AWS giới thiệu là xây dựng mô hình bảo mật Zero Trust – không tin tưởng mặc định bất kỳ ai hoặc hệ thống nào dù cho họ đang ở đâu.
### KIẾN TRÚC AWS VÀ CÁC NGUYÊN TẮC CỐT LÕI
Một trong những điểm mình thích ở mô hình Zero Trust trên AWS là hệ thống chia đường ống phòng thủ thành nhiều thành phần dựa trên các nguyên tắc chặt chẽ:
* **Xác minh và xác thực (Verify and authenticate)**: AWS yêu cầu mọi người dùng và hệ thống phải chứng minh danh tính liên tục trên từng request thay vì chỉ đăng nhập một lần. Hệ thống sẽ đánh giá các điểm dữ liệu như danh tính người dùng, vị trí, và trạng thái sức khỏe của thiết bị (device health) để cho phép truy cập.
* **Phân quyền tối thiểu (Least Privilege Access)**: Người dùng hoặc dịch vụ chỉ được cấp mức quyền tối thiểu cần thiết để hoàn thành công việc. AWS IAM Access Analyzer được sử dụng để phát hiện những quyền hạn đang bị cấp dư thừa (over-privileged) và giới hạn lại ranh giới chính xác.
* **Phân đoạn vi mô (Micro-segmentation)**: Dữ liệu và hệ thống được chia thành các phân đoạn nhỏ cách ly nghiêm ngặt với nhau. AWS Transit Gateway và Security Groups sẽ được dùng để cô lập các khối lượng công việc nhạy cảm (sensitive workloads) bên trong Amazon VPC.
* **Giám sát và Tự động hóa (Automation & Monitoring)**: Hệ thống được theo dõi liên tục để phát hiện và cảnh báo rủi ro. AWS GuardDuty và AWS Security Hub sẽ phân tích các sự kiện liên tục để tự động phát hiện mối đe dọa hoặc các hành vi bất thường.
### ZERO TRUST HOẠT ĐỘNG NHƯ THẾ NÀO?
Đây là phần mình thấy thú vị nhất.
Không phải tất cả các yêu cầu truy cập đều được cho qua một cách dễ dàng chỉ vì chúng nằm trong mạng nội bộ. Với mô hình này, hệ thống áp dụng nguyên lý "Assume Breach" – luôn giả định rằng hệ thống có thể đã bị xâm nhập và giới hạn mọi đường đi có thể.
Ví dụ, thay vì chỉ nhìn vào địa chỉ IP giống mạng truyền thống:
10.0.0.123
Hệ thống sẽ thực hiện đánh giá toàn diện các ngữ cảnh:
Identity: Role App-Backend
Action: s3:GetObject
Resource: arn:aws:s3: : :customer-data
Condition: MFA Active = True, Source VPC = vpc-12345
Từ những thông tin định danh này, hệ thống mới đưa ra quyết định có cho phép truy cập hay không, kể cả khi địa chỉ IP đang nằm trong vùng an toàn.
Đây là điểm khác biệt quan trọng: hệ thống bảo vệ dựa trên nhận dạng (identity) thay vì chỉ dựa vào chu vi mạng đơn giản (network boundary). Việc mã hóa dữ liệu và kiểm soát mạng cũng được thực thi nghiêm ngặt để giảm thiểu phạm vi ảnh hưởng (minimize blast radiuses).
### ĐIỀU MÌNH HỌC ĐƯỢC
Điều mình thấy đáng chú ý nhất sau khi đọc các tài liệu là góc nhìn cho rằng Zero Trust không phải là một công cụ có thể mua sẵn.
Điều quan trọng hơn là Zero Trust là một tư duy kiến trúc (mindset) cần được thiết kế sâu vào trong hệ thống đám mây. AWS IAM đảm nhận phần định danh cốt lõi. Các dịch vụ khác chịu trách nhiệm chia nhỏ mạng lưới, lưu lượng, giám sát và phản hồi tự động sự cố.
Có thể hình dung một luồng cơ bản:
Xác thực bằng IAM → Kiểm soát luồng mạng bằng Security Groups/VPC → Giám sát sự kiện bằng CloudTrail → Phân tích và phát hiện đe dọa bằng GuardDuty.
Mỗi service giải quyết một bài toán riêng, nhưng khi kết hợp lại chúng tạo thành một cơ chế phòng thủ chiều sâu (defense in depth) không thể bẻ gãy dễ dàng.
Qua đó, mình cũng nhận ra rằng khi xây dựng một ứng dụng đám mây thực tế, việc code chạy thành công chỉ là một phần nhỏ. Kiến trúc bảo mật xung quanh tài nguyên đám mây đó đóng vai trò sống còn.
### KẾT LUẬN
Các tài liệu về Zero Trust và Least Privilege cho mình một góc nhìn khá thú vị về cách kiến trúc lại bảo mật trên AWS.
Thay vì xây dựng một bức tường lửa lớn bên ngoài và để mọi thứ bên trong giao tiếp tự do, AWS hướng dẫn cách xác minh liên tục và thu hẹp tối đa đặc quyền truy cập trên từng dịch vụ. Đặc biệt, sự kết hợp giữa Quản lý danh tính (Identity) và Mạng (Networking) giúp loại bỏ triệt để điểm yếu lớn nhất của các mô hình bảo mật cũ.
Theo mình, đây là một tư duy rất đáng tìm hiểu đối với những ai đang học AWS, DevOps và muốn đem các nguyên tắc Security áp dụng trực tiếp vào các bài toán doanh nghiệp thực tế.
Nguồn tham khảo:
[1] AWS Security - Zero Trust on AWS (https://aws.amazon.com/security/zero-trust/)
[2] Zero Trust Security in AWS: Protecting Modern Cloud Infrastructure (https://builder.aws.com/content/3J2jVMOX6TBxqaso1EDDmXZxa8S/zero-trust-security-in-aws-protecting-modern-cloud-infrastructure)
[3] Understanding Zero Trust principles - AWS Prescriptive Guidance (https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-zero-trust-architecture/zero-trust-principles.html)

![](b1.jpg)

Link bài viết: https://www.facebook.com/groups/660548818043427/user/100053180442383
