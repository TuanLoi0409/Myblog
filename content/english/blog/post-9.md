---
title: "Mạng LAN: Nền tảng Kết nối Cục bộ"
meta_title: "Tổng quan về Mạng LAN (Local Area Network): Thành phần và Hoạt động"
description: "Bài viết chuyên sâu giải thích Mạng LAN là gì, các thành phần cốt lõi (Switch, Router, NIC), các loại cáp, địa chỉ MAC/IP, và các mô hình Topo mạng phổ biến."
date: 2025-09-24T05:00:00Z
image: "/images/LAN.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["LAN", "Ethernet"]
draft: false
---

**Mạng LAN** (viết tắt của **Local Area Network** - Mạng Cục bộ) là một hệ thống mạng máy tính được thiết kế để kết nối các thiết bị trong một phạm vi địa lý giới hạn và tương đối nhỏ. Phạm vi này có thể là một ngôi nhà, một văn phòng, một tòa nhà, hoặc một khuôn viên trường học.

Mục đích chính của mạng LAN là cho phép các thiết bị được kết nối (như máy tính, máy in, máy chủ, điện thoại, TV thông minh) có thể giao tiếp với nhau để **chia sẻ tài nguyên** (như tệp tin, máy in, kết nối Internet) và trao đổi thông tin một cách hiệu quả. Đây là loại mạng phổ biến và quen thuộc nhất với hầu hết người dùng.

LAN là nền tảng cơ bản, phân biệt rõ ràng với các loại mạng lớn hơn như **WAN** (Wide Area Network - Mạng Diện rộng, ví dụ như Internet) hay **MAN** (Metropolitan Area Network - Mạng Đô thị).

## Các thành phần cốt lõi của Mạng LAN

Để xây dựng một mạng LAN chức năng, chúng ta cần sự phối hợp của nhiều thành phần cả phần cứng lẫn phần mềm:

image: "/images/lan_components.jpg"

### 1. Thiết bị Đầu cuối (Endpoints)
Đây là các thiết bị mà người dùng tương tác trực tiếp, bao gồm:
* Máy tính cá nhân (PC), Laptop
* Máy chủ (Servers)
* Máy in mạng (Network Printers)
* Điện thoại IP (VoIP Phones)
* Camera an ninh (IP Cameras)
* Thiết bị di động (Smartphone, Tablet) kết nối qua Wi-Fi.

### 2. Card Giao diện Mạng (NIC - Network Interface Card)
Mỗi thiết bị đầu cuối cần có một NIC (thường gọi là "card mạng") để có thể kết nối vật lý với mạng. NIC cung cấp một cổng (ví dụ: cổng Ethernet RJ45) hoặc một bộ thu phát không dây (Wi-Fi). Mỗi NIC sở hữu một địa chỉ **MAC** (Media Access Control) 48-bit duy nhất trên toàn cầu, đóng vai trò như "số CMND" của thiết bị ở Lớp 2.

### 3. Phương tiện Truyền dẫn (Network Media)
Đây là "con đường" mà dữ liệu di chuyển qua:
* **Cáp xoắn đôi (Twisted Pair):** Phổ biến nhất là cáp Ethernet (ví dụ: Cat 5e, Cat 6). Chúng kết nối các thiết bị với Switch.
* **Sóng Vô tuyến (Radio Waves):** Được sử dụng bởi mạng **Wireless LAN (WLAN)**, hay còn gọi là Wi-Fi. Đây là một phần không thể thiếu của mạng LAN hiện đại.
* **Cáp quang (Fiber Optic):** Thường dùng cho các kết nối tốc độ cao, đường trục (backbone) trong các mạng LAN lớn (ví dụ: kết nối các Switch giữa các tầng).

### 4. Thiết bị Kết nối Mạng (Connectivity Devices)
Đây là các thiết bị trung tâm điều phối luồng dữ liệu:
* **Switch (Bộ chuyển mạch):** Trái tim của mạng LAN có dây hiện đại. Hoạt động ở Lớp 2, Switch sử dụng **địa chỉ MAC** để chuyển tiếp các khung dữ liệu (frames) một cách thông minh đến *đúng* cổng đích. Nó tạo ra các miền xung đột riêng biệt trên mỗi cổng, mang lại hiệu suất cao.
* **Access Point (AP - Điểm truy cập):** Là "Switch" của mạng không dây. Nó cho phép các thiết bị Wi-Fi kết nối vào mạng LAN có dây.
* **Router (Bộ định tuyến):** Hoạt động ở Lớp 3. Router có hai vai trò chính: 1) Kết nối mạng LAN của bạn với một mạng khác (thường là Internet/WAN). 2) **Định tuyến** (chuyển tiếp) lưu lượng giữa các mạng LAN con (VLAN) khác nhau. Trong mạng gia đình, Router Wi-Fi thường tích hợp cả 3 chức năng: Router, Switch (4 cổng LAN), và Access Point.
* **Hub (Bộ chia):** Thiết bị Lớp 1 đã lỗi thời, chỉ lặp lại tín hiệu đến tất cả các cổng, gây xung đột và lãng phí băng thông. Đã bị thay thế hoàn toàn bởi Switch.

## Mạng LAN hoạt động như thế nào?

Mạng LAN sử dụng hai hệ thống địa chỉ song song để đảm bảo dữ liệu đến đúng nơi:

### 1. Giao tiếp trong LAN (Layer 2 - Địa chỉ MAC)
Khi PC-A muốn gửi dữ liệu cho PC-B *trong cùng một mạng LAN*:
1.  PC-A biết địa chỉ IP của PC-B (ví dụ: `192.168.1.10`).
2.  PC-A cần địa chỉ MAC của PC-B. Nó sẽ gửi một tin nhắn **ARP** (Address Resolution Protocol) quảng bá (broadcast) ra toàn mạng, hỏi rằng: "Ai có IP `192.168.1.10`? Xin cho tôi địa chỉ MAC."
3.  PC-B nhận được tin nhắn, trả lời: "Tôi đây, MAC của tôi là `AA:BB:CC:11:22:33`."
4.  Switch sẽ "học" vị trí (cổng) của cả PC-A và PC-B vào Bảng MAC của nó.
5.  Từ lúc này, PC-A tạo một **Ethernet frame** với MAC đích là của PC-B. Khi Switch nhận được frame này, nó tra bảng MAC và chuyển frame *chỉ* đến cổng mà PC-B đang cắm, không làm phiền các thiết bị khác.

### 2. Giao tiếp ngoài LAN (Layer 3 - Địa chỉ IP)
Khi PC-A (IP: `192.168.1.20`) muốn gửi dữ liệu đến Google (IP: `8.8.8.8`):
1.  PC-A nhận ra rằng IP `8.8.8.8` không nằm trong mạng cục bộ `192.168.1.0/24` của nó.
2.  Thay vì tìm MAC của Google (điều này là không thể), PC-A sẽ gửi gói tin này đến **Default Gateway** (Cổng ra vào mặc định). Default Gateway chính là địa chỉ IP của **Router** (ví dụ: `192.168.1.1`).
3.  PC-A sẽ dùng ARP để tìm địa chỉ MAC của Router (`192.168.1.1`).
4.  Nó tạo một Ethernet frame với MAC đích là của **Router**, nhưng bên trong frame đó, gói tin IP vẫn có IP nguồn là PC-A và IP đích là Google.
5.  Router nhận được frame, "mở" nó ra, thấy gói tin IP dành cho `8.8.8.8`. Router sẽ tra bảng định tuyến của mình và chuyển tiếp gói tin này ra cổng WAN (hướng ra Internet).

## Mô hình Topo Mạng LAN (LAN Topologies)

Topo (topology) mô tả cách thức các thiết bị được kết nối vật lý hoặc logic.

image: "/images/lan_topologies.jpg"

### 1. Topo Hình Sao (Star Topology)
Đây là mô hình thống trị tuyệt đối trong các mạng LAN hiện đại.
* **Cách hoạt động:** Tất cả các thiết bị đầu cuối (PC, máy in) đều kết nối về một thiết bị trung tâm duy nhất (thường là **Switch**).
* **Ưu điểm:** Rất tin cậy. Nếu một cáp hoặc một thiết bị hỏng, nó chỉ ảnh hưởng đến đúng thiết bị đó; phần còn lại của mạng vẫn hoạt động bình thường. Dễ dàng thêm/bớt thiết bị và khắc phục sự cố.
* **Nhược điểm:** Nếu thiết bị trung tâm (Switch) bị hỏng, toàn bộ mạng sẽ sập. (Tuy nhiên, Switch hiện đại rất đáng tin cậy).

### 2. Topo Đường trục (Bus Topology)
Đây là mô hình cũ, sử dụng cáp đồng trục (coaxial cable).
* **Cách hoạt động:** Tất cả các thiết bị được kết nối trên cùng một sợi cáp chính (bus). Hai đầu cáp phải có thiết bị đầu cuối (terminator) để chống dội tín hiệu.
* **Nhược điểm:** Hiệu suất kém (vì là miền xung đột chung). Nếu cáp chính bị đứt ở bất kỳ đâu, toàn bộ mạng sẽ ngừng hoạt động. Rất khó khắc phục sự cố. (Đã lỗi thời).

### 3. Topo Vòng (Ring Topology)
Mô hình này (ví dụ: Token Ring của IBM) cũng đã lỗi thời.
* **Cách hoạt động:** Mỗi thiết bị kết nối chính xác với hai thiết bị khác, tạo thành một vòng khép kín. Dữ liệu chạy theo một chiều duy nhất qua một "thẻ bài" (token).
* **Nhược điểm:** Nếu một thiết bị hoặc một đoạn cáp trong vòng bị hỏng, toàn bộ vòng sẽ bị phá vỡ.

### 4. Wireless LAN (WLAN)
Đây thực chất là một biến thể của Topo Hình Sao, nơi các thiết bị (clients) kết nối không dây về một điểm trung tâm là **Access Point (AP)**. AP sau đó thường kết nối bằng dây vào Switch trung tâm của mạng LAN.

Tóm lại, mạng LAN là khối xây dựng cơ bản cho hầu hết mọi hoạt động kỹ thuật số. Từ việc chia sẻ máy in trong văn phòng nhỏ đến việc vận hành các trung tâm dữ liệu phức tạp, mạng LAN cung cấp kết nối tốc độ cao, đáng tin cậy, làm nền tảng cho việc truy cập tài nguyên và Internet.