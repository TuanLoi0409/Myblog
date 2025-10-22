---
title: "Switch: Trái tim của Mạng LAN Hiện đại"
meta_title: "Tìm hiểu về Switch Mạng: Cách hoạt động và Phân loại"
description: "Bài viết chuyên sâu về Switch (Bộ chuyển mạch), giải thích cách nó hoạt động ở Layer 2, vai trò của bảng MAC, sự khác biệt với Hub, và các loại Switch như Unmanaged, Managed, Layer 3."
date: 2025-08-16T05:00:00Z
image: "/images/Sw.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["LAN","Ethernet"]
draft: false
---

**Switch** (hay Bộ chuyển mạch) là một thiết bị mạng máy tính thông minh, đóng vai trò trung tâm trong việc kết nối các thiết bị trong một mạng cục bộ (LAN - Local Area Network). Nó hoạt động chủ yếu ở **Lớp 2 (Data Link Layer)** của mô hình OSI. Chức năng cơ bản của Switch là nhận các khung dữ liệu (Ethernet frames) từ một cổng (port), kiểm tra địa chỉ đích, và chuyển tiếp (forward) khung đó một cách thông minh đến *chỉ* cổng của thiết bị nhận, thay vì gửi đi khắp nơi.

Đây là một bước tiến hóa vượt bậc so với công nghệ cũ hơn là **Hub**. Trong khi Hub (thiết bị Lớp 1) chỉ đơn giản là sao chép mọi tín hiệu điện nhận được và phát lại chúng trên tất cả các cổng khác, Switch đưa ra quyết định dựa trên thông tin cụ thể. Điều này giúp giảm đáng kể lưu lượng mạng không cần thiết và loại bỏ hoàn toàn hiện tượng xung đột (collisions), giúp cải thiện đáng kể hiệu suất và bảo mật của mạng.

## Switch hoạt động như thế nào? Bảng Địa chỉ MAC

Sự "thông minh" của Switch nằm ở khả năng học và duy trì một bảng gọi là **Bảng Địa chỉ MAC** (MAC Address Table), hay còn gọi là CAM Table (Content Addressable Memory). Bảng này ánh xạ địa chỉ MAC (địa chỉ vật lý 48-bit duy nhất của mỗi card mạng) với cổng vật lý trên Switch mà thiết bị đó được kết nối.

Quá trình này diễn ra tự động:
1.  **Học (Learning):** Khi một thiết bị (ví dụ: PC-A) gửi một khung tin, Switch sẽ nhìn vào **địa chỉ MAC nguồn** của khung đó. Nó ghi lại rằng "MAC của PC-A được tìm thấy trên Cổng 1" và lưu thông tin này vào Bảng MAC.
2.  **Quyết định Chuyển tiếp (Forwarding/Filtering):** Khi Switch nhận một khung tin dành cho PC-B, nó sẽ tra cứu **địa chỉ MAC đích** trong Bảng MAC của mình.
    * **Nếu tìm thấy:** (Ví dụ: Bảng ghi rằng MAC của PC-B ở Cổng 5). Switch sẽ chỉ chuyển tiếp khung tin đó đến Cổng 5. Các cổng khác sẽ không nhận được khung tin này. Đây gọi là *filtering*.
    * **Nếu không tìm thấy (Unknown Unicast):** Nếu địa chỉ MAC đích chưa có trong bảng, Switch không biết PC-B ở đâu. Nó sẽ thực hiện hành động **Flood** (làm ngập) – gửi một bản sao của khung tin ra tất cả các cổng khác (trừ cổng nguồn).
    * **Broadcast:** Nếu khung tin có địa chỉ MAC đích là broadcast (ví dụ: `FF:FF:FF:FF:FF:FF`, thường dùng cho DHCP hoặc ARP), Switch sẽ luôn luôn *Flood* nó ra tất cả các cổng khác.



Theo thời gian, khi các thiết bị giao tiếp, Bảng MAC sẽ được xây dựng hoàn chỉnh, cho phép Switch tạo ra các "đường dẫn" riêng ảo, tạm thời giữa hai thiết bị đang giao tiếp.

## Sự khác biệt then chốt: Switch và Hub

Hiểu rõ sự khác biệt này là chìa khóa để hiểu giá trị của Switch:

| Đặc điểm | Hub (Bộ chia) | Switch (Bộ chuyển mạch) |
| :--- | :--- | :--- |
| **Lớp OSI** | Lớp 1 (Vật lý) | Lớp 2 (Liên kết dữ liệu) |
| **Cách xử lý** | "Dumb" - Lặp lại tín hiệu điện | "Intelligent" - Đọc địa chỉ MAC |
| **Chuyển tiếp** | Broadcast (Gửi đến mọi cổng) | Unicast (Gửi đến cổng đích cụ thể) |
| **Miền xung đột** | Một miền xung đột lớn (Tất cả thiết bị) | Mỗi cổng là một miền xung đột riêng |
| **Chế độ** | Half-duplex (Chỉ gửi *hoặc* nhận) | Full-duplex (Gửi *và* nhận đồng thời) |
| **Băng thông** | Bị chia sẻ cho tất cả thiết bị | Dành riêng cho mỗi cổng |



Kết quả là, một mạng dùng Switch có hiệu suất cao hơn nhiều, không còn xung đột và mỗi thiết bị có thể sử dụng toàn bộ băng thông của cổng (ví dụ: 1 Gbps) mà không phải tranh giành với các thiết bị khác.

## Phân loại Switch

Không phải tất cả các Switch đều giống nhau. Chúng được phân loại dựa trên khả năng quản lý và chức năng:

### 1. Unmanaged Switch (Switch không được quản lý)

Đây là loại cơ bản nhất, hoạt động theo kiểu "cắm và chạy" (plug-and-play). Chúng tự động học Bảng MAC và chuyển tiếp khung tin mà không cần bất kỳ cấu hình nào từ người dùng.
* **Ưu điểm:** Rẻ, đơn giản, dễ sử dụng.
* **Nhược điểm:** Không có tính năng nâng cao, không thể tùy chỉnh.
* **Sử dụng:** Phù hợp cho mạng gia đình, văn phòng nhỏ, hoặc mở rộng tạm thời số lượng cổng.

### 2. Managed Switch (Switch được quản lý)

Đây là các Switch cấp doanh nghiệp, cung cấp một giao diện (dòng lệnh CLI, giao diện web GUI, hoặc SNMP) để quản trị viên mạng có thể cấu hình và giám sát.
* **Ưu điểm:** Cung cấp vô số tính năng mạnh mẽ.
* **Nhược điểm:** Đắt hơn, yêu cầu kiến thức kỹ thuật để cấu hình.
* **Các tính năng chính:**
    * **VLAN (Virtual LANs):** Cho phép chia một Switch vật lý thành nhiều mạng logic ảo, riêng biệt. Đây là tính năng cơ bản để phân đoạn mạng, tăng cường bảo mật (ví dụ: tách mạng Kế toán khỏi mạng Khách).
    * **QoS (Quality of Service):** Cho phép ưu tiên một số loại lưu lượng mạng (như thoại VoIP, video call) hơn các loại khác (như download tệp).
    * **PoE (Power over Ethernet):** Khả năng cấp nguồn điện trực tiếp qua cáp Ethernet cho các thiết bị như điện thoại IP, camera an ninh, hoặc điểm truy cập Wi-Fi.
    * **Port Mirroring (SPAN):** Sao chép lưu lượng từ một cổng này sang một cổng khác để phục vụ cho việc giám sát, phân tích hoặc phát hiện xâm nhập (IDS).
    * **Spanning Tree Protocol (STP):** Một giao thức quan trọng để ngăn chặn vòng lặp (loops) trong mạng khi có nhiều đường kết nối dự phòng giữa các Switch.

### 3. Layer 3 Switch (Switch Lớp 3)

Còn được gọi là Switch đa lớp (Multilayer Switch). Đây là một thiết bị lai, kết hợp cả chức năng của Switch (Lớp 2) và Router (Lớp 3).
* Switch Lớp 3 có thể hiểu và đưa ra quyết định dựa trên **địa chỉ IP** (giống như Router).
* Chức năng chính của nó là **định tuyến (routing) giữa các VLAN** khác nhau với tốc độ cực cao (thực hiện bằng phần cứng chuyên dụng - ASIC), nhanh hơn nhiều so với việc sử dụng một Router truyền thống (router-on-a-stick).
* Chúng là thiết bị cốt lõi trong các mạng doanh nghiệp lớn, xử lý lưu lượng truy cập giữa các phòng ban hoặc các tầng khác nhau.

Tóm lại, Switch là thiết bị nền tảng không thể thiếu của mọi mạng LAN hiện đại. Từ các hộp nhựa đơn giản ở nhà đến các hệ thống khung gầm (chassis) phức tạp trong trung tâm dữ liệu, chúng thực hiện nhiệm vụ quan trọng là đảm bảo dữ liệu đến đúng nơi, một cách nhanh chóng và hiệu quả.