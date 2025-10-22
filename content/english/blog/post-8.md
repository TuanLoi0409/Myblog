---
title: "Hub: Thiết Bị Kết Nối Sơ Khai Của Mạng LAN"
meta_title: "Tìm hiểu về Hub Mạng: Hoạt động ở Lớp 1 và Miền Xung Đột"
description: "Bài viết giải thích Hub (Bộ chia) là gì, cách nó hoạt động ở Lớp 1 (Vật lý), tại sao nó tạo ra một miền xung đột lớn, và sự khác biệt cơ bản với Switch."
date: 2025-09-23T05:00:00Z
image: "/images/Hub.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["LAN","Ethernet"]
draft: false
---

**Hub** (hay Bộ chia) là một trong những thiết bị mạng máy tính cơ bản và sơ khai nhất, được sử dụng để kết nối các thiết bị trong một mạng cục bộ (LAN). Nó hoạt động ở **Lớp 1 (Physical Layer)** của mô hình OSI. Chức năng cơ bản của Hub là nhận các tín hiệu điện (biểu thị cho các bit dữ liệu) từ một cổng (port) và phát lại (repeat) tín hiệu đó ra *tất cả* các cổng khác, mà không cần quan tâm đến nội dung hay đích đến của dữ liệu.

Hub thường được coi là một thiết bị "ngớ ngẩn" (dumb device) vì nó không có khả năng xử lý hay ra quyết định. Nó không đọc được địa chỉ MAC hay địa chỉ IP. Nó chỉ đơn thuần là một bộ lặp tín hiệu đa cổng (multi-port repeater).

Đây là công nghệ tiền nhiệm của **Switch**. Trong khi Switch tạo ra các liên kết riêng ảo giữa người gửi và người nhận, Hub chỉ đơn giản là tạo ra một "không gian" chung nơi mọi thiết bị đều "la" vào và mọi thiết bị khác đều "nghe" thấy.

## Hub hoạt động như thế nào? Miền Xung Đột

Sự "ngớ ngẩn" của Hub nằm ở cơ chế hoạt động đơn giản của nó:

1.  **Nhận tín hiệu:** Một thiết bị (PC-A) gửi một luồng bit (tín hiệu điện) đến một cổng trên Hub.
2.  **Phát lại (Repeat):** Hub nhận tín hiệu này, có thể tái tạo (regenerate) lại cho mạnh hơn (nếu là Active Hub), và ngay lập tức sao chép và gửi tín hiệu đó ra *tất cả các cổng còn lại*.
3.  **Tiếp nhận:** Mọi thiết bị khác (PC-B, PC-C, PC-D...) kết nối với Hub đều nhận được tín hiệu này. Thiết bị nào thấy dữ liệu này không phải dành cho mình (sau khi card mạng của nó xử lý ở Lớp 2) sẽ tự động hủy bỏ gói tin.

image: "/images/hub_broadcasting.jpg"

Cơ chế này dẫn đến hai vấn đề nghiêm trọng:

* **Miền Xung Đột (Collision Domain):** Vì mọi thứ được gửi đến mọi nơi, nếu PC-A và PC-B cùng gửi dữ liệu *chính xác cùng một lúc*, tín hiệu điện của chúng sẽ va chạm (collide) trên đường truyền. Dữ liệu của cả hai sẽ bị hỏng. Toàn bộ các thiết bị kết nối vào Hub tạo thành **một miền xung đột duy nhất**.
* **Chế độ Bán Song Công (Half-Duplex):** Do nguy cơ xung đột, các thiết bị trên mạng Hub phải tuân theo quy tắc CSMA/CD (Carrier Sense Multiple Access with Collision Detection). Nói đơn giản, một thiết bị chỉ có thể *gửi* HOẶC *nhận* tại một thời điểm, không bao giờ được cả hai.
* **Băng thông chia sẻ (Shared Bandwidth):** Nếu Hub có băng thông 100 Mbps, thì 100 Mbps này bị *chia sẻ* cho tất cả các thiết bị. Nếu 10 thiết bị cùng hoạt động, chúng phải tranh giành nhau để sử dụng chung 100 Mbps đó, khiến hiệu suất mạng giảm sút nghiêm trọng.

## Sự khác biệt then chốt: Hub và Switch

Hiểu rõ sự khác biệt này là lý do tại sao Hub trở nên lỗi thời:

| Đặc điểm | Hub (Bộ chia) | Switch (Bộ chuyển mạch) |
| :--- | :--- | :--- |
| **Lớp OSI** | **Lớp 1 (Vật lý)** | Lớp 2 (Liên kết dữ liệu) |
| **Cách xử lý** | "Dumb" - Lặp lại tín hiệu điện | "Intelligent" - Đọc địa chỉ MAC |
| **Chuyển tiếp** | **Broadcast** (Gửi đến mọi cổng) | Unicast (Gửi đến cổng đích cụ thể) |
| **Miền xung đột** | **Một miền xung đột lớn** (Tất cả thiết bị) | Mỗi cổng là một miền xung đột riêng |
| **Chế độ** | **Half-duplex** (Chỉ gửi *hoặc* nhận) | Full-duplex (Gửi *và* nhận đồng thời) |
| **Băng thông** | **Bị chia sẻ** cho tất cả thiết bị | **Dành riêng** cho mỗi cổng |

Kết quả là, một mạng dùng Switch có hiệu suất cao hơn gấp nhiều lần, không còn xung đột, và mỗi thiết bị có thể sửu dụng toàn bộ băng thông của cổng.

## Phân loại Hub

Trong thời kỳ của mình, Hub được chia làm hai loại chính:

image: "/images/active_vs_passive_hub.jpg"

### 1. Passive Hub (Hub thụ động)

Đây là loại Hub đơn giản nhất, không cần cắm nguồn điện. Nó chỉ đơn thuần kết nối vật lý các dây dẫn của cáp mạng lại với nhau, cho phép tín hiệu truyền qua. Nó không tái tạo hay khuếch đại tín hiệu, do đó tín hiệu sẽ bị suy hao và giới hạn khoảng cách cáp. Chúng cực kỳ hiếm.

### 2. Active Hub (Hub chủ động)

Đây là loại Hub phổ biến. Chúng cần nguồn điện ngoài để hoạt động. Chức năng quan trọng nhất của chúng là hoạt động như một **bộ lặp (repeater)**. Khi nhận một tín hiệu yếu, nó sẽ *tái tạo* (regenerate) và *khuếch đại* tín hiệu đó về lại mức chuẩn trước khi gửi đi, cho phép cáp mạng đạt được chiều dài tối đa (ví dụ: 100 mét cho cáp Ethernet).

Tóm lại, Hub là một thiết bị lịch sử quan trọng, đặt nền móng cho mạng LAN. Tuy nhiên, do những hạn chế cố hữu về hiệu suất, xung đột và băng thông, nó đã bị **thay thế hoàn toàn** bởi Switch trong hầu hết mọi ứng dụng mạng hiện đại. Ngày nay, gần như không thể tìm thấy Hub được bán mới cho mục đích sửo dụng thông thường.