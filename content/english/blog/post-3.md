---
title: "Phân loại Mã độc Android bằng Decision Tree"
meta_title: "Hướng dẫn Phân loại Mã độc Android bằng Cây Quyết định"
description: "Phân tích chi tiết quy trình sử dụng thuật toán Cây Quyết định (Decision Tree) trong Machine Learning để phát hiện và phân loại các họ mã độc trên nền tảng Android."
date: 2025-06-20T05:00:00Z
image: "/images/malware.jpg"
categories: ["Machine Learning"]
author: "Tuấn Lợi"
tags: ["Malware"]
draft: false
---

Với hệ sinh thái mở và thị phần khổng lồ, Android đã trở thành mục tiêu hàng đầu cho các tác nhân độc hại. Mỗi ngày, hàng ngàn ứng dụng độc hại mới được tạo ra, từ phần mềm quảng cáo (adware) đơn giản đến phần mềm gián điệp (spyware) và ransomware tinh vi. Việc phát hiện thủ công là không thể. Đây là lúc Machine Learning (Học máy) và cụ thể là thuật toán **Cây Quyết định (Decision Tree)** phát huy vai trò của mình.

Phân loại mã độc không chỉ dừng lại ở việc xác định một ứng dụng là "lành tính" (benign) hay "độc hại" (malicious). Mục tiêu sâu hơn là phân loại ứng dụng độc hại đó thuộc về *họ* (family) nào (ví dụ: Joker, Anubis, FakeApp). Cây Quyết định là một mô hình học máy có giám sát (supervised learning) mạnh mẽ, hoạt động bằng cách xây dựng một cấu trúc giống như lưu đồ, nơi mỗi nút bên trong đại diện cho một "bài kiểm tra" về một đặc trưng (feature) và mỗi nút lá (leaf) đại diện cho một nhãn phân loại (class label).

## Tại sao Cây Quyết định lại hữu ích cho việc này?

Khác biệt với các mô hình "hộp đen" (black box) như mạng nơ-ron sâu (Deep Learning), lợi thế lớn nhất của Cây Quyết định là **tính diễn giải (interpretability)**.

Khi mô hình được huấn luyện xong, bạn có thể "đọc" chính cái cây đó. Bạn có thể thấy rõ ràng các quy tắc mà mô hình đã học được. Ví dụ, một nhánh của cây có thể là: "NẾU ứng dụng yêu cầu quyền `SEND_SMS` VÀ yêu cầu quyền `READ_CONTACTS` VÀ kết nối đến địa chỉ IP `123.45.67.89` THÌ phân loại là `Spyware_Family_X`". Khả năng này cực kỳ giá trị đối với các nhà phân tích bảo mật, giúp họ hiểu *tại sao* một mẫu bị gắn cờ, thay vì chỉ biết *rằng* nó bị gắn cờ.



## Quy trình Xây dựng Mô hình Phân loại

Việc xây dựng một mô hình Cây Quyết định để phân loại mã độc Android bao gồm nhiều bước then chốt:

### 1. Thu thập và Gắn nhãn Dữ liệu

Đầu tiên, chúng ta cần một bộ dữ liệu (dataset) lớn gồm các tệp `.apk` (tệp cài đặt Android).
* **Mẫu độc hại:** Thu thập từ các nguồn công khai như Androzoo, MalGenome, Drebin, hoặc VirusTotal. Các mẫu này phải được gắn nhãn chính xác về họ mã độc (ví dụ: Adware, Trojan, Spyware).
* **Mẫu lành tính:** Thu thập từ các cửa hàng ứng dụng chính thức như Google Play Store.

### 2. Trích xuất Đặc trưng (Feature Engineering)

Đây là bước quan trọng nhất. Chúng ta không thể đưa thẳng tệp `.apk` vào mô hình. Thay vào đó, chúng ta phải "dịch" mỗi ứng dụng thành một vector các con số (features). Có hai phương pháp chính:

* **Phân tích tĩnh (Static Analysis):** Phân tích tệp `.apk` mà không cần chạy nó. Đây là phương pháp phổ biến nhất cho Cây Quyết định. Các đặc trưng bao gồm:
    * **Quyền (Permissions):** Các quyền được yêu cầu trong tệp `AndroidManifest.xml` (ví dụ: `android.permission.SEND_SMS`, `android.permission.READ_CONTACTS`, `android.permission.INSTALL_PACKAGES`). Đây là những đặc trưng cực kỳ mạnh.
    * **API Calls:** Các lệnh gọi hàm API nhạy cảm được tìm thấy trong mã (ví dụ: `getDeviceId`, `SmsManager`, `Socket`).
    * **Intent Filters:** Các hành động mà ứng dụng đăng ký nhận (ví dụ: `BOOT_COMPLETED`).
    * **Opcodes:** Các mã lệnh Dalvik (tương tự mã máy).

* **Phân tích động (Dynamic Analysis):** Chạy ứng dụng trong một môi trường ảo hóa (sandbox) và giám sát hành vi của nó:
    * Lưu lượng mạng (Network traffic): Các IP/domain mà nó kết nối đến.
    * Hành vi hệ thống (System calls): Các tệp nó tạo/xóa, các quy trình nó khởi chạy.
    * Phương pháp này chính xác hơn nhưng tốn thời gian và tài nguyên hơn nhiều.

### 3. Huấn luyện Mô hình

Sau khi trích xuất, chúng ta có một bảng dữ liệu khổng lồ, trong đó mỗi hàng là một ứng dụng và mỗi cột là một đặc trưng (với giá trị 1/0 hoặc đếm số lần xuất hiện).

Dữ liệu này được chia thành tập huấn luyện (training set) và tập kiểm thử (test set). Thuật toán Cây Quyết định (như ID3, C4.5, hoặc **CART**) sẽ "học" từ tập huấn luyện. Nó làm điều này bằng cách tìm ra đặc trưng nào (ví dụ: `permission.SEND_SMS`) có khả năng phân tách dữ liệu thành các nhóm "thuần khiết" nhất (ví dụ: một nhóm toàn mã độc và một nhóm toàn ứng dụng lành tính). Quá trình này được lặp lại đệ quy, tạo ra các nhánh và nút, cho đến khi cây hoàn thành.

> Điểm mạnh cốt lõi của Cây Quyết định không nằm ở việc đạt độ chính xác cao nhất so với các mô hình phức tạp, mà nằm ở khả năng cung cấp các quy tắc rõ ràng, dễ hiểu. Một nhà phân tích bảo mật có thể kiểm tra trực tiếp các quy tắc mà cây đã học để xác thực các phát hiện hoặc khám phá các mẫu hành vi độc hại mới.

### 4. Đánh giá và Tinh chỉnh

Sau khi cây được xây dựng, chúng ta sử dụng tập kiểm thử (dữ liệu mà mô hình chưa từng thấy) để đánh giá hiệu suất của nó thông qua các chỉ số như **Accuracy** (Độ chính xác), **Precision**, **Recall**, và **F1-Score**. Một ma trận nhầm lẫn (confusion matrix) sẽ cho thấy rõ mô hình hay nhầm lẫn giữa các họ mã độc nào.

Một vấn đề phổ biến là **quá khớp (overfitting)**, nơi cây trở nên quá phức tạp và "học thuộc lòng" dữ liệu huấn luyện. Kỹ thuật "cắt tỉa" (pruning) thường được sử dụng để loại bỏ các nhánh không cần thiết, giúp mô hình tổng quát hóa tốt hơn.

Tóm lại, Cây Quyết định là một công cụ mạnh mẽ và mang tính nền tảng trong lĩnh vực phân loại mã độc Android. Mặc dù các mô hình như Random Forest (bản chất là nhiều Cây Quyết định) hoặc Deep Learning có thể đạt độ chính xác cao hơn, Cây Quyết định vẫn là lựa chọn tuyệt vời cho các hệ thống cần sự minh bạch, tốc độ và khả năng diễn giải.