---
title: "Java RMI: Trái tim của Lập trình Phân tán trong Java"
meta_title: "Tìm hiểu về Java RMI: Kiến trúc và Cách hoạt động"
description: "Bài viết chuyên sâu về Java RMI (Remote Method Invocation), giải thích kiến trúc phân tán, vai trò của Stub và Skeleton, và cách nó cho phép Java object gọi phương thức trên máy ảo khác."
date: 2025-10-18T05:00:00Z
image: "/images/Rmi.jpg"
categories: ["Networking"]
author: "Tuấn Lợi"
tags: ["Java"]
draft: false
---

**Java RMI** (Remote Method Invocation - Gọi phương thức từ xa) là một API của Java cho phép một đối tượng đang chạy trong một Máy ảo Java (JVM) này có thể gọi các phương thức của một đối tượng đang chạy trong một JVM khác—thậm chí trên một máy tính khác. RMI mang lại cơ chế Lập trình thủ tục từ xa (RPC) mạnh mẽ nhưng với đặc trưng hướng đối tượng của Java.

Nó hoạt động ở **Lớp Ứng dụng (Application Layer)** và cho phép lập trình viên xây dựng các ứng dụng phân tán mà không cần phải quan tâm nhiều đến việc lập trình socket phức tạp hay cơ chế truyền/nhận dữ liệu thô. Chức năng cơ bản của RMI là làm cho việc gọi một phương thức trên một đối tượng ở xa (remote object) trở nên *trong suốt* (transparent), giống hệt như đang gọi một phương thức trên một đối tượng cục bộ (local object).

Đây là một bước tiến hóa so với việc lập trình mạng truyền thống dùng **Sockets**. Trong khi Sockets (Lớp 4) chỉ đơn giản là gửi và nhận các luồng byte (stream) thô, RMI hoạt động ở mức độ đối tượng. Nó tự động xử lý việc "đóng gói" (marshalling) các tham số đối tượng, gửi chúng qua mạng, "mở gói" (unmarshalling) ở phía nhận, và gọi phương thức thực tế, sau đó trả về kết quả theo quy trình ngược lại.

## RMI hoạt động như thế nào? Stub, Skeleton và Registry

Sự "thông minh" của RMI nằm ở ba thành phần cốt lõi: **Stub** (phía Client), **Skeleton** (phía Server), và **RMI Registry** (dịch vụ tra cứu).

image: "/images/rmi_architecture_flow.jpg"

Quá trình này diễn ra như sau:

1.  **Đăng ký (Binding):** Phía Server, một đối tượng từ xa (Remote Object) được tạo ra. Server sau đó "đăng ký" hoặc "bind" đối tượng này với một tên duy nhất vào **RMI Registry**. Registry là một dịch vụ tên (naming service) đơn giản, giống như một danh bạ điện thoại, ánh xạ tên dịch vụ (ví dụ: "CalculatorService") với đối tượng từ xa.
2.  **Tra cứu (Lookup):** Phía Client, ứng dụng muốn sử dụng dịch vụ từ xa. Nó liên hệ với RMI Registry trên máy chủ và yêu cầu "tra cứu" (lookup) đối tượng dựa trên tên đã đăng ký (ví dụ: "CalculatorService").
3.  **Lấy Stub:** Nếu tìm thấy, Registry sẽ trả về cho Client một đối tượng đặc biệt gọi là **Stub**. Stub là một đối tượng proxy (ủy nhiệm) phía client, nó triển khai cùng một interface (giao diện) với đối tượng từ xa. Đối với Client, Stub *chính là* đối tượng từ xa.
4.  **Gọi phương thức (Invocation):**
    * Client gọi một phương thức trên đối tượng Stub (ví dụ: `stub.add(5, 10)`).
    * Stub không thực thi logic. Thay vào đó, nó thực hiện **Marshalling**: nó đóng gói tên phương thức và các tham số (số 5 và 10) thành một thông điệp.
    * Stub gửi thông điệp này qua mạng đến phía Server (nơi có đối tượng thật).
5.  **Thực thi (Execution):**
    * Trên Server, một **Skeleton** (hoặc trình RMI runtime) lắng nghe các yêu cầu. Nó nhận thông điệp từ Stub.
    * Skeleton thực hiện **Unmarshalling**: nó giải nén thông điệp để biết phương thức nào cần gọi (`add`) và các tham số là gì (5 và 10).
    * Skeleton gọi phương thức `add(5, 10)` trên **Đối tượng thực thi (Remote Object)**.
6.  **Trả về kết quả:** Đối tượng thực thi trả về kết quả (ví dụ: 15). Skeleton nhận kết quả này, Marshalling nó và gửi trở lại cho Stub. Stub Unmarshalling kết quả và trả nó về cho mã Client.

Toàn bộ quá trình phức tạp này được RMI che giấu hoàn toàn khỏi lập trình viên.

## Sự khác biệt then chốt: RMI và Sockets

Hiểu rõ sự khác biệt này là chìa khóa để thấy giá trị của RMI:

| Đặc điểm | Socket (Lập trình TCP/IP) | Java RMI (Gọi phương thức từ xa) |
| :--- | :--- | :--- |
| **Lớp trừu tượng** | Lớp Vận tải (Layer 4) | Lớp Ứng dụng (Layer 7) |
| **Cách xử lý** | "Thô" - Gửi/nhận luồng byte | "Thông minh" - Gửi/nhận đối tượng |
| **Giao tiếp** | Lập trình viên phải tự định nghĩa giao thức | Dựa trên Giao diện (Interface) và Phương thức |
| **Serialization** | Phải tự xử lý (chuyển đổi đối tượng thành byte) | Tự động (Marshalling/Unmarshalling) |
| **Mức độ phức tạp** | Rất phức tạp, dễ lỗi | Đơn giản hơn nhiều cho ứng dụng phân tán |
| **Mục đích** | Giao tiếp mạng mức thấp, linh hoạt | Gọi phương thức Java-to-Java |

Kết quả là, một ứng dụng dùng RMI dễ phát triển và bảo trì hơn nhiều, cho phép lập trình viên tập trung vào logic nghiệp vụ thay vì cơ chế truyền thông mạng.

## Các thành phần chính của Kiến trúc RMI

Một ứng dụng RMI hoàn chỉnh được cấu tạo từ các phần sau:

image: "/images/rmi_components.jpg"

### 1. Remote Interface (Giao diện từ xa)

Đây là bản "hợp đồng" cốt lõi. Nó là một interface Java kế thừa từ `java.rmi.Remote`. Mọi phương thức trong interface này (mà client sẽ gọi) đều phải khai báo ném ra `java.rmi.RemoteException`.
* **Ví dụ:** `public interface Calculator extends Remote { int add(int a, int b) throws RemoteException; }`

### 2. Remote Object Implementation (Lớp thực thi)

Đây là lớp phía Server, nơi chứa logic nghiệp vụ thực sự. Lớp này phải `implements` (triển khai) Remote Interface.
* Thông thường, nó kế thừa từ `java.rmi.server.UnicastRemoteObject` để xử lý các hành vi của đối tượng từ xa (như lắng nghe, export...).
* **Ví dụ:** `public class CalculatorImpl extends UnicastRemoteObject implements Calculator { ... }`

### 3. Stub và Skeleton (Các lớp Proxy)

Như đã giải thích, **Stub** là proxy phía client, **Skeleton** là proxy phía server. Trong các phiên bản Java hiện đại (từ Java 1.5 trở đi), Skeleton không còn cần thiết một cách tường minh; RMI runtime sẽ tự động xử lý phía server bằng cách sử dụng reflection. Stub vẫn là thành phần thiết yếu phía client.

### 4. RMI Registry (Dịch vụ Đăng ký)

Là một server tên đơn giản (thường chạy trên cổng 1099). Nó giống như một `Map` toàn cục nơi các server đăng ký dịch vụ của mình (`bind`) và các client tra cứu chúng (`lookup`).

### 5. Client (Ứng dụng Khách)

Là chương trình Java thực hiện `lookup` RMI Registry để lấy về đối tượng Stub, sau đó gọi các phương thức trên Stub đó như thể nó là một đối tượng cục bộ.

Tóm lại, Java RMI là một công nghệ nền tảng, đơn giản hóa mạnh mẽ việc xây dựng các hệ thống phân tán trong Java. Bằng cách trừu tượng hóa toàn bộ lớp giao vận mạng phức tạp và thay thế nó bằng cơ chế gọi phương thức hướng đối tượng quen thuộc, RMI cho phép các ứng dụng Java giao tiếp với nhau một cách hiệu quả và minh bạch.