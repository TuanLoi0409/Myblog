---
title: "Ethernet: Chuẩn Mực Toàn Cầu cho Mạng Có Dây"
meta_title: "Phân tích Sâu về Ethernet: Từ Khung dữ liệu đến Switches và VLANs"
description: "Một bài phân tích chi tiết về Ethernet, bao gồm lịch sử, cấu trúc khung Ethernet, các loại cáp, sự khác biệt cốt lõi giữa Hub và Switch, và các công nghệ mở rộng như VLAN."
date: 2025-10-07T05:00:00Z
image: "/images/ethernet.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["LAN","Ethernet"]
draft: false
---

**Ethernet** là một tập hợp các công nghệ mạng máy tính được sử dụng rộng rãi nhất cho các mạng cục bộ (LAN - Local Area Networks). Nó được chuẩn hóa thành **IEEE 802.3** và xác định các quy tắc cho việc kết nối vật lý (Lớp 1 - Physical Layer) và cách các thiết bị chia sẻ, truy cập vào mạng (Lớp 2 - Data Link Layer). Từ các văn phòng, gia đình, đến các trung tâm dữ liệu khổng lồ, Ethernet là nền tảng cho gần như mọi kết nối mạng có dây ngày nay.

Lịch sử của Ethernet bắt đầu từ những năm 1970 tại trung tâm nghiên cứu Xerox PARC, nơi Robert Metcalfe và các đồng nghiệp đã phát triển nó. Phiên bản ban đầu sử dụng cáp đồng trục (coaxial cable) theo cấu trúc liên kết hình bus (bus topology), nghĩa là tất cả các thiết bị được kết nối trên cùng một đường truyền vật lý.

## Cấu trúc Khung Ethernet (Ethernet Frame)

Để gửi dữ liệu qua mạng, Ethernet gói dữ liệu (thường là một gói IP) vào một đơn vị gọi là **Khung** (Frame). Khung Ethernet giống như một chiếc phong bì, chứa không chỉ dữ liệu mà còn thông tin kiểm soát quan trọng để đảm bảo nó đến đúng nơi.

Một khung Ethernet II (loại phổ biến nhất) bao gồm các thành phần chính:
* **Preamble & SFD (Start Frame Delimiter):** 7 byte Preamble và 1 byte SFD được sử dụng để đồng bộ hóa đồng hồ giữa các thiết bị gửi và nhận, báo hiệu rằng một khung tin sắp bắt đầu.
* **Destination MAC Address (6 byte):** Địa chỉ MAC của thiết bị nhận.
* **Source MAC Address (6 byte):** Địa chỉ MAC của thiết bị gửi.
* **EtherType (2 byte):** Một mã cho biết loại giao thức Lớp 3 nào đang được mang trong Payload (ví dụ: `0x0800` cho IPv4, `0x0806` cho ARP).
* **Payload (46 - 1500 byte):** Đây là nơi chứa dữ liệu thực tế, ví dụ như một gói tin IP. Ethernet yêu cầu payload phải có kích thước tối thiểu là 46 byte.
* **FCS (Frame Check Sequence - 4 byte):** Một giá trị checksum được tính toán dựa trên nội dung của khung. Thiết bị nhận sẽ tính toán lại checksum; nếu nó không khớp, khung tin sẽ bị hủy bỏ (coi như bị lỗi).



## CSMA/CD và Sự tiến hóa của Phần cứng

Trong các mạng Ethernet đời đầu dùng cáp đồng trục hoặc **Hub** (thiết bị Lớp 1), tất cả các thiết bị đều nằm trong cùng một "miền xung đột" (collision domain). Chúng sử dụng một cơ chế gọi là **CSMA/CD** (Carrier Sense Multiple Access with Collision Detection):
1.  **Carrier Sense:** Nghe (cảm nhận) đường truyền xem có ai đang gửi không.
2.  **Multiple Access:** Nếu đường truyền rảnh, bắt đầu gửi.
3.  **Collision Detection:** Vẫn tiếp tục nghe trong khi gửi. Nếu phát hiện tín hiệu của mình bị "lấn át" bởi tín hiệu khác (xung đột xảy ra), nó sẽ:
    * Ngừng gửi ngay lập tức.
    * Gửi một tín hiệu "Jam" để thông báo cho mọi thiết bị khác biết là đã có xung đột.
    * Chờ một khoảng thời gian ngẫu nhiên (sử dụng thuật toán backoff) trước khi thử lại từ bước 1.

> Việc sử dụng Hub và CSMA/CD rất không hiệu quả vì mạng chỉ có thể hoạt động ở chế độ **half-duplex** (chỉ gửi hoặc nhận tại một thời điểm) và hiệu suất giảm nghiêm trọng khi có nhiều thiết bị.

Cuộc cách mạng thực sự là sự ra đời của **Switch** (Bộ chuyển mạch). Switch là một thiết bị Lớp 2 thông minh:
* **Học địa chỉ MAC:** Switch xây dựng một bảng (MAC Address Table) ghi lại địa chỉ MAC của thiết bị nào đang được kết nối với cổng (port) nào.
* **Chuyển mạch thông minh:** Khi nhận được một khung tin, Switch đọc địa chỉ MAC đích. Thay vì gửi đi tất cả các cổng như Hub, nó chỉ chuyển tiếp khung tin đến đúng cổng nơi có thiết bị đích.
* **Phân đoạn Miền xung đột:** Quan trọng nhất, mỗi cổng trên một Switch là một miền xung đột riêng biệt. Điều này loại bỏ hoàn toàn nhu cầu về CSMA/CD.
* **Full-Duplex:** Vì không còn xung đột, các thiết bị kết nối với Switch có thể gửi và nhận dữ liệu đồng thời (**full-duplex**), tăng gấp đôi thông lượng hiệu quả (ví dụ: 100 Mbps gửi và 100 Mbps nhận cùng lúc).

## Phương tiện truyền dẫn và Tốc độ

Ethernet đã phát triển qua nhiều tốc độ, từ 10 Mbps (Mega-bit trên giây) ban đầu, lên 100 Mbps (Fast Ethernet), 1 Gbps (Gigabit Ethernet), và giờ đây là 10 Gbps, 40 Gbps, 100 Gbps và cao hơn nữa.

Sự phát triển này được hỗ trợ bởi các loại cáp khác nhau:
* **Cáp đồng xoắn đôi (Twisted Pair - UTP/STP):** Phổ biến nhất là cáp UTP (không bọc giáp), như **Cat 5e**, **Cat 6**, và **Cat 6a**. Chúng sử dụng đầu nối RJ45 và là tiêu chuẩn cho kết nối máy tính để bàn, camera IP, và các thiết bị trong văn phòng.
* **Cáp quang (Fiber Optic):** Sử dụng ánh sáng để truyền dữ liệu qua các sợi thủy tinh. Cáp quang hỗ trợ tốc độ cực cao và khoảng cách xa hàng km, miễn nhiễm với nhiễu điện từ. Nó là lựa chọn bắt buộc cho các kết nối đường trục (backbone) giữa các tòa nhà, giữa các tầng trong tòa nhà lớn, và trong các trung tâm dữ liệu.