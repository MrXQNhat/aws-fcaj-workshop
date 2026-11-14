---
title: "Worklog Tuần 12"
date: 2026-12-06
weight: 2
chapter: false
pre: " <b> 1.12 </b> "
---


### Mục tiêu tuần 12:

* Nắm vững các nguyên tắc cơ bản về bảo mật container, kỹ thuật thoát khỏi container và tăng cường bảo mật runtime.
* Khám phá bảo mật kiến trúc cụm Kubernetes (K8s), các lỗi cấu hình sai RBAC và phương pháp thoát khỏi pod.

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                     | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Bảo mật Docker & Thoát khỏi Container (Escape) <br>&emsp; + Phân tích rủi ro lộ Docker socket <br>&emsp; + Mô phỏng thoát khỏi container qua privileged container                                   | 30/11/2026 | 30/11/2026      | <https://www.cisecurity.org/benchmark/docker> |
| 3   | - Bảo mật Kiến trúc Cụm Kubernetes <br>&emsp; + Khám phá thành phần K8s control plane và bảo mật API server <br>&emsp; + Kiểm toán quyền Kiểm soát Truy cập Dựa trên Vai trò (RBAC)                     | 01/12/2026 | 01/12/2026      | <https://kubernetes.io/docs/concepts/security/> |
| 4   | - Khai thác Cấu hình sai Kubernetes <br>&emsp; + Khai thác các role RBAC và service account cấp quyền quá mức <br>&emsp; + Lạm dụng xác thực ẩn danh và K8s dashboard không an toàn         | 02/12/2026 | 02/12/2026      | <https://book.hacktricks.xyz/pentesting-cloud/kubernetes-security-pentesting> |
| 5   | - Pod Breakout & Di chuyển Ngang (Lateral Movement) <br>&emsp; + Thực hiện kỹ thuật pod breakout (lạm dụng host network/PID namespace) <br>&emsp; + Đánh cắp secret từ etcd hoặc các object secret của K8s | 03/12/2026 | 03/12/2026      | <https://falco.org/>                      |
| 6   | - Tăng cường Bảo mật & Phòng thủ Kubernetes <br>&emsp; + Triển khai Pod Security Standards (PSS) và Network Policies <br>&emsp; + Thiết lập admission controller và giám sát runtime security (Falco)  | 04/12/2026 | 04/12/2026      | <https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/2716980/nsa-cisa-release-kubernetes-hardening-guidance/> |

### Kết quả đạt được tuần 12:

* Đạt được kiến thức toàn diện về ranh giới cách ly container và các lỗi cấu hình Docker phổ biến cho phép thoát container ra host.
* Đánh giá trạng thái bảo mật cụm Kubernetes bằng cách kiểm toán cấu hình RBAC và nhận diện các service account bị lạm dụng quyền.
* Thực thi các cuộc tấn công mô phỏng trên Kubernetes, bao gồm khai thác endpoint API bị lộ và lạm dụng thông số cấu hình pod kém an toàn.
* Thực hành các kỹ thuật pod breakout nâng cao như thao tác namespace và lợi dụng việc mount volume nhạy cảm.
* Triển khai các giải pháp tăng cường bảo mật container bao gồm Pod Security Standards, Network Policies và giám sát thời gian thực với Falco.