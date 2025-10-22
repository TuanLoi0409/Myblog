---
title: "Socket UDP"
meta_title: "Tìm hiểu về Giao thức UDP trong Lập trình Mạng"
description: "Phân tích chi tiết về giao thức UDP (User Datagram Protocol), đặc điểm, cấu trúc, và các trường hợp sử dụng phổ biến trong lập trình mạng."
date: 2025-10-04T05:00:00Z
image: "/images/udp.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["Java"]
draft: false
---

UDP, viết tắt của **User Datagram Protocol**, là một trong những giao thức truyền tải (Transport Layer) cốt lõi của bộ giao thức Internet (IP). Đây là một giao thức "không kết nối" (connectionless), có nghĩa là nó không thiết lập một kênh giao tiếp riêng biệt hay một phiên (session) ổn định trước khi bắt đầu gửi dữ liệu.

Trong lập trình mạng, UDP nổi bật với đặc tính đơn giản, hiệu suất cao và độ trễ cực kỳ thấp. Nó không cung cấp các cơ chế đảm bảo của TCP, chẳng hạn như xác nhận gói tin, truyền lại gói tin bị mất, hay sắp xếp lại các gói tin đến sai thứ tự. Vì lý do này, nó thường được gọi là giao thức "gửi và quên" (send and forget). Trách nhiệm xử lý lỗi và đảm bảo tính toàn vẹn dữ liệu được giao hoàn toàn cho tầng ứng dụng (Application Layer).

## Cấu trúc Header của UDP

Một trong những lý do khiến UDP nhanh là vì phần header (tiêu đề) của nó rất nhỏ và đơn giản, chỉ chiếm **8 byte**. Cấu trúc của nó bao gồm bốn trường:

1.  **Source Port** (16 bit): Số hiệu cổng của ứng dụng gửi. Trường này là tùy chọn; nếu không dùng, nó sẽ được đặt về 0.
2.  **Destination Port** (16 bit): Số hiệu cổng của ứng dụng nhận. Đây là trường bắt buộc.
3.  **Length** (16 bit): Tổng độ dài (tính bằng byte) của toàn bộ datagram, bao gồm cả 8 byte header và dữ liệu (payload).
4.  **Checksum** (16 bit): Dùng để kiểm tra lỗi của cả header và dữ liệu. Việc sử dụng Checksum là tùy chọn trong IPv4 nhưng là bắt buộc trong IPv6.

## Đặc điểm và Ứng dụng

Trái ngược với TCP, UDP không thực hiện "bắt tay ba bước" (three-way handshake). Một ứng dụng có thể tạo một gói UDP và gửi nó đi ngay lậpf tức mà không cần chờ phản hồi từ máy nhận.

> Đặc tính không đáng tin cậy của UDP thực chất lại là một lợi thế lớn trong nhiều kịch bản. Đối với các ứng dụng ưu tiên tốc độ và thời gian thực hơn là sự chính xác tuyệt đối, việc dừng lại để truyền lại một gói tin bị mất (như TCP làm) có thể gây ra hiện tượng giật, lag, làm hỏng trải nghiệm người dùng.

Các ứng dụng tiêu biểu của UDP bao gồm:

* **Game trực tuyến:** Nơi các cập nhật trạng thái (vị trí người chơi, hành động) cần được gửi liên tục và nhanh chóng. Mất một vài gói tin còn tốt hơn là bị trễ (lag) do chờ truyền lại.
* **Streaming Video/Audio (VoIP):** Như gọi điện thoại qua Internet (Zalo, Skype) hoặc xem video trực tiếp. Việc mất một vài mili giây âm thanh hoặc một vài pixel hình ảnh thường chấp nhận được, miễn là luồng dữ liệu không bị gián đoạn.
* **DNS (Domain Name System):** Các truy vấn phân giải tên miền cần phải nhanh và gọn. Một truy vấn DNS chỉ là một gói tin nhỏ gửi đi và một gói tin nhỏ nhận về. Dùng TCP cho việc này sẽ gây lãng phí tài nguyên.
* **Broadcasting và Multicasting:** UDP cho phép gửi một gói tin đến nhiều máy nhận cùng lúc, một tính năng mà TCP không hỗ trợ.

## Lập trình Socket với UDP

Trong lập trình mạng, sự khác biệt giữa TCP và UDP là rất rõ ràng:

* **Với TCP (hướng kết nối):** Bạn sử dụng các socket `STREAM`. Server phải `listen()` (lắng nghe) và `accept()` (chấp nhận) một kết nối đến từ client. Sau khi kết nối, hai bên giao tiếp qua một luồng (stream) dữ liệu hai chiều ổn định.
* **Với UDP (không kết nối):** Bạn sử dụng các socket `DGRAM` (datagram).
    * **Server** chỉ cần `bind()` socket của mình vào một cổng (port) cụ thể và chờ nhận dữ liệu bằng hàm `recvfrom()`. Hàm này sẽ trả về cả dữ liệu và địa chỉ (IP, port) của bên gửi.
    * **Client** không cần `connect()`. Mỗi khi muốn gửi dữ liệu, client chỉ cần dùng hàm `sendto()` và chỉ định rõ địa chỉ (IP, port) của server nhận trong mỗi lần gửi.

Vì không có "kết nối", một server UDP có thể nhận dữ liệu từ nhiều client khác nhau trên cùng một socket mà không cần quản lý nhiều phiên kết nối phức tạp.