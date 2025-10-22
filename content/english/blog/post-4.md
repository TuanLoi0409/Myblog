---
title: "Lập trình Socket TCP"
meta_title: "Hướng dẫn Chuyên sâu về Lập trình Socket TCP (Hướng kết nối)"
description: "Bài viết chuyên sâu về giao thức TCP và cách lập trình socket TCP (hướng kết nối) trong các ứng dụng mạng, bao gồm quy trình 3-way handshake, luồng dữ liệu, và quản lý kết nối."
date: 2025-10-01T05:00:00Z
image: "/images/Tcp.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["Java"]
draft: false
---

**Transmission Control Protocol (TCP)** là một trong những giao thức cốt lõi và quan trọng nhất của bộ giao thức Internet. Không giống như UDP (Giao thức Datagram Người dùng) hoạt động theo kiểu "gửi và quên", TCP được thiết kế cho **độ tin cậy**. Nó là một giao thức **hướng kết nối** (connection-oriented) và **dựa trên luồng** (stream-based), cung cấp cơ chế đảm bảo rằng mọi byte dữ liệu được gửi đi sẽ đến được đích, đúng thứ tự và không bị lỗi.

Hầu như mọi ứng dụng web mà chúng ta sử dụng hàng ngày—từ việc duyệt web (HTTP/HTTPS), gửi email (SMTP), đến truyền tệp (FTP) hay đăng nhập từ xa (SSH)—đều chạy trên nền tảng vững chắc của TCP. Khi lập trình mạng, việc hiểu rõ cách TCP thiết lập và duy trì một "kết nối" là điều tối quan trọng.

## Cốt lõi của TCP: Kết nối Tin cậy

Sự tin cậy của TCP không phải là phép thuật; nó được xây dựng trên một loạt các cơ chế thông minh. Toàn bộ quá trình bắt đầu ngay cả trước khi byte dữ liệu đầu tiên được gửi.

### 1. Bắt tay Ba bước (3-Way Handshake)

Trước khi Client và Server có thể trao đổi dữ liệu, chúng phải thiết lập một kết nối. Quá trình này được gọi là "bắt tay ba bước" và nó xác nhận rằng cả hai bên đều sẵn sàng và có thể giao tiếp:
1.  **SYN (Synchronize):** Client gửi một gói tin đặc biệt gọi là **SYN** đến Server. Gói tin này về cơ bản nói rằng: "Tôi muốn thiết lập kết nối. Số thứ tự (sequence number) ban đầu của tôi là X."
2.  **SYN-ACK (Synchronize-Acknowledge):** Server, sau khi nhận được SYN, sẽ gửi lại một gói tin **SYN-ACK**. Gói tin này có nghĩa: "Tôi đã nhận được yêu cầu kết nối của bạn (ACK cho số X+1). Tôi cũng đồng ý kết nối. Số thứ tự ban đầu của tôi là Y."
3.  **ACK (Acknowledge):** Cuối cùng, Client gửi lại một gói tin **ACK** để xác nhận đã nhận được SYN-ACK của Server: "Tôi đã nhận được xác nhận của bạn (ACK cho số Y+1). Kết nối đã được thiết lập."



Sau khi bước 3 hoàn tất, một kết nối ảo, song công (full-duplex) được thiết lập. Cả hai bên đều biết bên kia đang hoạt động và sẵn sàng nhận dữ liệu.

### 2. Truyền dữ liệu Tin cậy và Sắp xếp Thứ tự

Một khi kết nối được thiết lập, TCP chia dữ liệu của ứng dụng thành các đoạn (segments) có kích thước phù hợp. Mỗi segment được gán một **số thứ tự (sequence number)**.
* **Đảm bảo thứ tự:** Bên nhận sử dụng các số thứ tự này để sắp xếp lại các segment về đúng thứ tự ban đầu, ngay cả khi chúng đến nơi một cách lộn xộn do các tuyến đường mạng khác nhau.
* **Xác nhận (ACKs):** Khi bên nhận nhận được một segment, nó sẽ gửi lại một gói tin xác nhận (**ACK**) cho bên gửi, báo rằng "Tôi đã nhận thành công dữ liệu cho đến byte thứ N."
* **Truyền lại (Retransmission):** Bên gửi duy trì một bộ đếm thời gian. Nếu nó không nhận được ACK cho một segment trong một khoảng thời gian nhất định, nó sẽ coi segment đó là bị mất và **tự động gửi lại**.

### 3. Kiểm soát Luồng (Flow Control)

TCP cũng quản lý tốc độ truyền dữ liệu. Mỗi bên (cả client và server) đều có một bộ đệm (buffer) để lưu trữ dữ liệu đến. Nếu một ứng dụng đọc dữ liệu chậm, bộ đệm của nó có thể bị đầy.

> Thông qua một cơ chế gọi là **Receive Window** (Cửa sổ Nhận), bên nhận liên tục quảng bá cho bên gửi biết nó còn bao nhiêu dung lượng bộ đệm trống. Nếu bên nhận báo "cửa sổ" của tôi bằng 0, bên gửi sẽ dừng gửi dữ liệu (ngoại trừ các gói tin thăm dò) cho đến khi nhận được thông báo rằng cửa sổ đã mở trở lại. Điều này ngăn chặn bên gửi làm quá tải (overwhelm) bên nhận.

## Quy trình Lập trình Socket TCP

Trong lập trình, giao diện cho TCP là **Socket**. Một socket là một điểm cuối (endpoint) của giao tiếp hai chiều. Quy trình làm việc của Server và Client khác nhau rõ rệt.

### Quy trình phía Server (Bị động)

Server là bên "lắng nghe" các kết nối đến.
1.  **`socket()`:** Server tạo một socket (ví dụ: `AF_INET` cho IPv4 và `SOCK_STREAM` cho TCP).
2.  **`bind()`:** Server liên kết (bind) socket này với một địa chỉ IP cụ thể (hoặc tất cả các địa chỉ có sẵn) và một số hiệu cổng (port number) (ví dụ: port 80 cho HTTP, port 443 cho HTTPS). Đây là "địa chỉ" mà client sẽ dùng để tìm đến server.
3.  **`listen()`:** Server đặt socket vào chế độ lắng nghe. Nó cũng chỉ định một `backlog`—số lượng kết nối có thể được xếp hàng chờ đợi trong khi server đang bận xử lý một kết nối khác.
4.  **`accept()`:** Đây là một hàm **khóa (blocking call)**. Chương trình server sẽ "ngủ" tại đây cho đến khi có một client thực hiện `connect()` (hoàn tất 3-way handshake). Khi một kết nối được chấp nhận, hàm `accept()` sẽ trả về một **socket hoàn toàn mới**.

Điều quan trọng cần hiểu là: Socket lắng nghe ban đầu (listening socket) *không* dùng để trao đổi dữ liệu. Nó chỉ có một nhiệm vụ duy nhất là chấp nhận các kết nối mới. Mỗi khi `accept()` thành công, nó tạo ra một *socket kết nối* (connected socket) mới. Toàn bộ việc `send()` và `recv()` với client đó sẽ diễn ra trên socket mới này. Socket lắng nghe ngay lập tức quay lại chờ đợi kết nối tiếp theo. Đây là cách một server có thể xử lý nhiều client cùng lúc (thường bằng cách tạo một luồng hoặc tiến trình mới cho mỗi socket kết nối).



### Quy trình phía Client (Chủ động)

Client là bên chủ động bắt đầu kết nối.
1.  **`socket()`:** Client cũng tạo một socket (`AF_INET`, `SOCK_STREAM`).
2.  **`connect()`:** Client gọi hàm `connect()`, cung cấp địa chỉ IP và số hiệu cổng của Server mà nó muốn kết nối. Lời gọi hàm này sẽ **khởi tạo quá trình 3-way handshake**. Đây cũng là một hàm **khóa**; nó sẽ không trả về cho đến khi kết nối được thiết lập thành công hoặc bị lỗi (ví dụ: server không phản hồi, kết nối bị từ chối).
3.  **`send()` / `recv()`:** Một khi `connect()` trả về thành công, kết nối đã được thiết lập. Client giờ đây có thể tự do gửi dữ liệu đến server bằng `send()` và nhận dữ liệu bằng `recv()`.

## TCP là một Luồng (Stream)

Một khái niệm quan trọng trong lập trình TCP là **luồng (stream)**. TCP không quan tâm đến "ranh giới" của các gói tin.

* **Phía gửi:** Nếu bạn gọi `send()` hai lần, một lần với "Hello" và một lần với "World", hệ điều hành có thể gộp chúng lại và gửi đi trong một segment duy nhất, hoặc chia "Hello" thành hai segment.
* **Phía nhận:** Bên nhận không biết bạn đã gọi `send()` bao nhiêu lần. Nó chỉ nhận được một luồng byte liên tục. Khi gọi `recv()`, nó có thể nhận được "HelloWor", và lần gọi `recv()` tiếp theo sẽ nhận được "ld".

Do đó, ứng dụng của bạn phải tự chịu trách nhiệm trong việc đóng gói (framing) dữ liệu. Ví dụ, bạn có thể quy ước rằng 4 byte đầu tiên của mỗi tin nhắn là độ dài của tin nhắn đó, hoặc sử dụng một ký tự phân cách đặc biệt (như `\n` trong nhiều giao thức text-based).

### Đóng kết nối (4-Way Handshake)

Khi một bên (ví dụ: client) đã gửi xong dữ liệu, nó gọi `close()` trên socket. Điều này bắt đầu "bắt tay 4 bước" để đóng kết nối, đảm bảo cả hai bên đều biết rằng không còn dữ liệu nào nữa. TCP đảm bảo rằng tất cả dữ liệu đang chờ trong bộ đệm sẽ được gửi đi trước khi kết nối thực sự bị đóng.

Tóm lại, TCP cung cấp một lớp trừu tượng mạnh mẽ. Nó ẩn đi mọi sự phức tạp của một mạng lưới không đáng tin cậy (mất gói, sai thứ tự) và cung cấp cho lập trình viên một đường ống (pipe) giao tiếp ảo, tin cậy, hai chiều, giống như đọc và ghi vào một tệp tin. Sự đánh đổi cho độ tin cậy này là độ trễ cao hơn (do handshake) và chi phí header lớn hơn so với UDP.