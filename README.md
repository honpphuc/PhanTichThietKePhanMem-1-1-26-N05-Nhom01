# Phân Tích và Thiết Kế Hệ Thống Thương Mại Điện Tử Tiki.vn

> **Học phần:** Phân tích và Thiết kế Phần mềm  
> **Trường:** Trường Công nghệ Thông tin Phenikaa – Đại học Phenikaa  
> **Giảng viên hướng dẫn:** TS. Trương Đức Phương  
> **Nhóm thực hiện:** Nhóm 10  

---

## Thành viên nhóm

| STT | Họ và tên | Vai trò / Phần phụ trách |
| :-: | :--- | :--- |
| 1 | **Lê Hồng Phúc** | Xác định yêu cầu, Mô hình Use Case & Đặc tả chi tiết |
| 2 | **Nguyễn Thành Đô** | Phân tích kiến trúc hệ thống, Biểu đồ Sequence, Activity & BCE |
| 3 | **Nguyễn Hoàng Thiên** | Thiết kế hệ thống, Biểu đồ Lớp (Class Diagram), CSDL (ERD) & UI/UX |
| 4 | **Phạm Ngọc Vũ** | Đánh giá hệ thống, Kết luận, Hướng phát triển & Tổng hợp tài liệu |

---

## 1. Giới thiệu đề tài

**Tiki.vn** là một trong những sàn thương mại điện tử (TMĐT) tổng hợp hàng đầu tại Việt Nam, vận hành theo mô hình lai (**Hybrid**) kết hợp giữa:
- **Tiki Trading (1P):** Tiki trực tiếp nhập hàng, lưu kho (FBT - Fulfillment by Tiki), xuất bán và cam kết hàng chính hãng.
- **Marketplace (3P):** Sàn giao dịch cho phép các nhà bán hàng độc lập đăng ký gian hàng, quản lý sản phẩm, tồn kho và xử lý đơn hàng qua **Seller Center**.

Dự án này áp dụng quy trình phân tích và thiết kế hướng đối tượng sử dụng ngôn ngữ mô hình hóa thống nhất (**UML 2.5**) để khảo sát, phân tích nghiệp vụ và thiết kế kiến trúc phần mềm hoàn chỉnh cho nền tảng Tiki.vn.

### Phạm vi hệ thống

| Trong phạm vi (In-Scope) | Ngoài phạm vi (Out-of-Scope) |
| :--- | :--- |
| • Quản lý tài khoản, phân quyền, sổ địa chỉ<br>• Danh mục đa cấp, tìm kiếm, bộ lọc, xem chi tiết SP<br>• Giỏ hàng, đặt hàng, thanh toán, tách đơn tự động<br>• Khuyến mãi, mã giảm giá (Voucher), Tiki Xu, TikiNOW<br>• Theo dõi vận đơn, hủy đơn, đổi trả & hoàn tiền<br>• Đánh giá sản phẩm, hỏi đáp, chat với nhà bán/CSKH<br>• Kênh người bán (Seller Center): gian hàng, SP, tồn kho, đơn hàng, đối soát<br>• Quản trị nội bộ (Admin Portal): người dùng, kiểm duyệt, danh mục, khiếu nại, thống kê | • Dịch vụ tài chính mở rộng (Tiki Card, bảo hiểm, vay tiêu dùng)<br>• Dịch vụ vé máy bay, nạp thẻ điện thoại, thanh toán hóa đơn<br>• Thuật toán gợi ý AI/Machine Learning chuyên sâu<br>• Quản lý kho vật lý nội bộ (WMS chi tiết) của đơn vị vận chuyển<br>• Hệ thống ERP kế toán, thuế, nhân sự nội bộ của Tiki<br>• Hệ thống đấu thầu quảng cáo (Tiki Ads) & Livestream |

---

## 2. Xác định yêu cầu & Mô hình Use Case (Phần 1)

Hệ thống được khảo sát và chuẩn hóa với:
- **43 Yêu cầu chức năng (FR01 – FR43)** chia theo các phân hệ nghiệp vụ.
- **23 Yêu cầu phi chức năng (NFR01 – NFR23):** Đảm bảo thời gian phản hồi $\le 3$ giây, chịu tải cao trong các đợt Flash Sale, độ sẵn sàng $\ge 99.5\%$, bảo mật thanh toán chuẩn PCI DSS và tính toàn vẹn giao dịch ACID chống bán vượt tồn kho (overselling).
- **15 Quy tắc nghiệp vụ cốt lõi (BR01 – BR15):** Quy định chặt chẽ về tồn kho theo SKU, cơ chế tách đơn hàng tổng thành các kiện giao hàng (Shipment) theo từng nhà bán, điều kiện hủy đơn/đổi trả và đối soát doanh thu.

### Các tác nhân (Actors)
1. **Khách vãng lai (Guest):** Duyệt danh mục, tìm kiếm, xem chi tiết sản phẩm, đăng ký, đăng nhập (`UC01` – `UC05`).
2. **Khách hàng (Customer):** Kế thừa quyền của Guest; quản lý giỏ hàng, đặt hàng, thanh toán, áp mã giảm giá/Tiki Xu, theo dõi/hủy đơn, đổi trả, đánh giá, quản lý hồ sơ/sổ địa chỉ, yêu thích, chat (`UC06` – `UC16`).
3. **Nhà bán hàng (Seller):** Đăng ký gian hàng, quản lý sản phẩm & SKU, xử lý đơn hàng, xử lý đổi trả, xem báo cáo doanh thu, tạo khuyến mãi shop (`UC12`, `UC16`, `UC17` – `UC22`).
4. **Quản trị viên (Admin):** Quản lý người dùng, duyệt gian hàng/sản phẩm, quản lý danh mục & khuyến mãi toàn sàn, giải quyết khiếu nại/hoàn tiền, thống kê (`UC17`, `UC23` – `UC27`).
5. **Nhân viên CSKH:** Hỗ trợ khách hàng, xử lý hội thoại chat, tiếp nhận đổi trả và phân xử khiếu nại (`UC12`, `UC16`, `UC26`).
6. **Hệ thống bên ngoài:**
   - **Cổng thanh toán (Payment Gateway):** VNPAY, MoMo, ZaloPay, Thẻ ATM/Visa (`UC08`, `UC12`, `UC26`).
   - **Đơn vị vận chuyển (Shipping Partner):** Nhận bàn giao hàng, cập nhật hành trình, thu hồi hàng trả (`UC10`, `UC12`, `UC19`).
   - **Dịch vụ SMS/Email:** Gửi mã xác thực OTP và thông báo đơn hàng (`UC01`, `UC02`, `UC07`).

### Danh sách 27 Use Case của hệ thống

| Mã UC | Tên Use Case | Nhóm phân hệ | Quan hệ đặc biệt |
| :-: | :--- | :--- | :--- |
| `UC01` – `UC02` | Đăng ký, Đăng nhập | Tài khoản & Xác thực | Tích hợp SMS/Email OTP |
| `UC03` – `UC05` | Tìm kiếm SP, Xem danh mục, Xem chi tiết SP | Khám phá sản phẩm | Hỗ trợ lọc, sắp xếp, chọn SKU |
| `UC06` | Quản lý giỏ hàng | Giỏ hàng & Đặt hàng | Nhóm sản phẩm theo Gian hàng |
| `UC07` | Đặt hàng | Giỏ hàng & Đặt hàng | `«include»` `UC08`, `«extend»` `UC09` |
| `UC08` | Thanh toán | Thanh toán | Tích hợp Cổng thanh toán / COD |
| `UC09` | Áp mã giảm giá / Tiki Xu | Khuyến mãi | Mở rộng từ `UC07` |
| `UC10` | Theo dõi đơn hàng | Vận chuyển & Hậu mãi | Nhận trạng thái từ ĐVVC |
| `UC11` | Hủy đơn hàng | Vận chuyển & Hậu mãi | `«extend»` `UC10` |
| `UC12` | Yêu cầu đổi trả / hoàn tiền | Vận chuyển & Hậu mãi | `«include»` `UC10` |
| `UC13` – `UC16` | Đánh giá SP, Quản lý địa chỉ, Yêu thích, Chat/Hỏi đáp | Cá nhân & Tương tác | Kết nối Customer – Seller – CSKH |
| `UC17` – `UC22` | Đăng ký shop, Quản lý SP, Xử lý đơn, Đổi trả, Doanh thu, KM Shop | Seller Center | Tương tác giữa Seller, Admin & ĐVVC |
| `UC23` – `UC27` | Quản lý User, Duyệt Shop/SP, Danh mục & KM, Khiếu nại, Thống kê | Quản trị (Admin Portal) | Phối hợp Admin, CSKH & Cổng TT |

---

## 3. Phân tích kiến trúc hệ thống (Phần 2)

### Kiến trúc phân tầng (4-Tier Architecture)
Hệ thống được thiết kế theo kiến trúc 4 lớp nhằm tách biệt giao diện, logic nghiệp vụ và lưu trữ dữ liệu:
1. **Lớp Trình diễn (Presentation Layer):** Website khách hàng (`tiki.vn`), Ứng dụng di động (iOS/Android), Cổng người bán (`sellercenter.tiki.vn`) và Trang quản trị (`Admin Portal`).
2. **Lớp Nghiệp vụ (Business / Application Logic Layer):** Bao gồm các dịch vụ cốt lõi (User, Product, Cart, Order, Payment, Promotion, Review) và các cổng giao tiếp chuẩn với hệ thống ngoài (`ICongThanhToan`, `IDonViVanChuyen`, `INotificationService`).
3. **Lớp Truy xuất dữ liệu (Data Access Layer):** Quản lý ORM/Repository, Connection Pool và đảm bảo tính toàn vẹn giao dịch (Database Transactions).
4. **Lớp Cơ sở dữ liệu (Database Layer):** Hệ quản trị CSDL quan hệ (RDBMS), kết hợp Kho tìm kiếm (**Search Index** phục vụ truy vấn từ khóa/bộ lọc tốc độ cao) và Bộ nhớ đệm (**Cache** giảm tải trong các khung giờ Flash Sale).

### Hiện thực hóa Use Case (Use-Case Realizations)
Báo cáo đi sâu vào phân tích mô hình **BCE (Boundary – Control – Entity)**, **Biểu đồ trình tự (Sequence Diagrams)** và **Biểu đồ hoạt động (Activity Diagrams)** cho các luồng nghiệp vụ trọng tâm như:
- Luồng *Tìm kiếm sản phẩm* (`TimKiemUI` $\rightarrow$ `TimKiemController` $\rightarrow$ `SanPham` / `NganhHang`).
- Luồng *Đặt hàng & Thanh toán* (`DatHangUI` $\rightarrow$ `DatHangController` $\rightarrow$ `DonHang` / `ICongThanhToan`), bao gồm kiểm tra tồn kho, áp dụng Voucher, giữ tồn kho tạm thời và xử lý kết quả giao dịch.

---

## 4. Thiết kế hệ thống & Cơ sở dữ liệu (Phần 3)

### 4.1. Các hệ thống con (Subsystems)
Hệ thống được chia thành 9 hệ thống con độc lập tương tác qua giao diện API:
1. Hệ thống con Quản lý tài khoản
2. Hệ thống con Tìm kiếm và Khám phá sản phẩm
3. Hệ thống con Quản lý sản phẩm
4. Hệ thống con Giỏ hàng và Đặt hàng
5. Hệ thống con Thanh toán
6. Hệ thống con Vận chuyển và Theo dõi đơn hàng
7. Hệ thống con Đánh giá và Chăm sóc khách hàng
8. Hệ thống con Quản lý người bán (Seller Center)
9. Hệ thống con Quản trị hệ thống (Admin Portal)

### 4.2. Thiết kế Cơ sở dữ liệu (16 Bảng cốt lõi trong ERD)

| STT | Tên bảng | Khóa chính (PK) | Mô tả chức năng |
| :-: | :--- | :--- | :--- |
| 1 | `User` | `user_id` | Lưu thông tin tài khoản chung và thông tin xác thực |
| 2 | `Customer` | `customer_id` | Lưu thông tin hồ sơ khách hàng mua sắm |
| 3 | `Seller` | `seller_id` | Lưu thông tin nhà bán hàng trên Marketplace |
| 4 | `Admin` | `admin_id` | Lưu thông tin quản trị viên vận hành hệ thống |
| 5 | `Shop` | `shop_id` | Lưu thông tin gian hàng của người bán |
| 6 | `Category` | `category_id` | Lưu cấu trúc cây danh mục và phân loại sản phẩm |
| 7 | `Product` | `product_id` | Lưu thông tin chung của sản phẩm đăng bán |
| 8 | `SKU` | `sku_id` | Lưu các phiên bản/biến thể cụ thể (màu sắc, kích cỡ, giá) của sản phẩm |
| 9 | `Inventory` | `inventory_id` | Quản lý số lượng tồn kho khả dụng và tồn kho giữ chỗ theo từng SKU |
| 10 | `Cart` | `cart_id` | Lưu thông tin giỏ hàng của khách hàng |
| 11 | `CartItem` | `cart_item_id` | Lưu danh sách SKU và số lượng sản phẩm được chọn trong giỏ |
| 12 | `Order` | `order_id` | Lưu thông tin đơn hàng, địa chỉ giao, tổng tiền và trạng thái xử lý |
| 13 | `OrderItem` | `order_item_id` | Lưu chi tiết từng SKU, số lượng và đơn giá tại thời điểm đặt mua |
| 14 | `Payment` | `payment_id` | Lưu thông tin phương thức, mã giao dịch cổng thanh toán và trạng thái |
| 15 | `Shipping` | `shipping_id` | Lưu thông tin vận đơn/kiện hàng tách theo gian hàng và trạng thái giao nhận |
| 16 | `Review` | `review_id` | Lưu điểm đánh giá sao và nhận xét của khách hàng đối với sản phẩm |

---

## 5. Cấu trúc tài liệu báo cáo

Dự án bao gồm các phần nội dung chính trong báo cáo tổng hợp (`PhanTichThietKePhanMem_BTL_Nhom10.docx`):

```text
BTL_PTTKPM_Nhom10_Tiki
 ┣  PhanTichThietKePhanMem_BTL_Nhom10.docx   # Báo cáo tổng hợp hoàn chỉnh
 ┣  Bản Vẽ                                   #Chứa các bản vẽ do nhóm thực hiện để phục vụ dự án
 ┗  README.md                                # Tài liệu giới thiệu tổng quan dự án
```

---

## 6. Đánh giá & Hướng phát triển tương lai (Phần 4)

### Ưu điểm của thiết kế
- **Tách biệt rõ ràng (Separation of Concerns):** Áp dụng kiến trúc 4 lớp và mô hình BCE giúp dễ dàng bảo trì, nâng cấp từng phân hệ độc lập.
- **Quản lý biến thể & tồn kho chuẩn TMĐT:** Tách riêng `Product` – `SKU` – `Inventory` giúp kiểm soát chính xác giá bán và số lượng tồn kho của từng phân loại hàng hóa.
- **Hỗ trợ mô hình Marketplace đa nhà bán:** Cơ chế giỏ hàng và tách gói giao hàng (`Shipping` / `Shipment`) đáp ứng tốt bài toán một đơn hàng chứa sản phẩm từ nhiều gian hàng khác nhau.

### Định hướng nâng cấp
1. **Chuyển đổi sang Microservices & Event-Driven Architecture (EDA):** Tách hoàn toàn các Subsystem thành các dịch vụ độc lập có CSDL riêng (`Database-per-service`), giao tiếp bất đồng bộ qua Message Broker (Kafka/RabbitMQ) để chịu tải hàng triệu giao dịch/ngày.
2. **Hoàn thiện hệ sinh thái Đa kho (Multi-warehouse & Smart Order Routing):** Tự động định tuyến đơn hàng tới kho TikiNOW (FBT) hoặc kho nhà bán gần địa chỉ khách hàng nhất.
3. **Tích hợp AI/ML Recommendation & Social Commerce:** Bổ sung công cụ gợi ý sản phẩm cá nhân hóa theo thời gian thực, đánh giá kèm video ngắn và tiếp thị liên kết (Affiliate Marketing).

---

## 7. Tài liệu tham khảo
1. Trang chủ sàn TMĐT Tiki: [https://tiki.vn](https://tiki.vn)
2. Cổng thông tin người bán Tiki Seller Center: [https://sellercenter.tiki.vn](https://sellercenter.tiki.vn)
3. Tiki Engineering Blog: [https://medium.com/tiki-engineering](https://medium.com/tiki-engineering)
4. Object Management Group (OMG) – *Unified Modeling Language (UML) Specification*: [https://www.omg.org/spec/UML/](https://www.omg.org/spec/UML/)
5. Microservice Architecture Patterns: [https://microservices.io](https://microservices.io)
