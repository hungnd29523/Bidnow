# Tài Liệu Cơ Sở Dữ Liệu - BidNow

Tài liệu này mô tả chi tiết tất cả các bảng trong cơ sở dữ liệu của hệ thống BidNow, bao gồm mục đích và chức năng của từng trường.

---

## 1. Bảng Users (Người Dùng)

**Mục đích**: Lưu trữ thông tin tài khoản của tất cả người dùng trong hệ thống (người mua, người bán, admin).

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của người dùng |
| `email` | nvarchar(255), Unique | Email đăng nhập, phải duy nhất trong hệ thống |
| `password_hash` | nvarchar(255) | Mật khẩu đã được mã hóa (hash) |
| `full_name` | nvarchar(255) | Họ và tên đầy đủ của người dùng |
| `phone` | nvarchar(20), Nullable | Số điện thoại liên hệ |
| `avatar_url` | nvarchar(500), Nullable | URL ảnh đại diện của người dùng |
| `reputation_score` | decimal(3,2), Default: 0.00 | Điểm uy tín của người dùng (0.00 - 5.00), tính từ các đánh giá |
| `total_ratings` | int, Default: 0 | Tổng số lượt đánh giá đã nhận |
| `total_sales` | int, Default: 0 | Tổng số sản phẩm đã bán thành công |
| `total_purchases` | int, Default: 0 | Tổng số sản phẩm đã mua thành công |
| `is_active` | bool, Default: true | Trạng thái tài khoản (true = hoạt động, false = bị khóa) |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo tài khoản |

**Quan hệ**:
- Một User có thể có nhiều Auctions (với vai trò Seller)
- Một User có thể thắng nhiều Auctions (với vai trò Winner)
- Một User có thể có nhiều Bids, AutoBids, Messages, Notifications, Ratings, Watchlists, Orders, Disputes
- Một User có thể có nhiều UserRoles (phân quyền)

---

## 2. Bảng Categories (Danh Mục)

**Mục đích**: Lưu trữ các danh mục sản phẩm để phân loại items và auctions.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của danh mục |
| `name` | nvarchar(100) | Tên danh mục (ví dụ: "Điện Thoại", "Laptop") |
| `slug` | nvarchar(100), Unique | URL-friendly name cho danh mục (ví dụ: "dien-thoai") |
| `description` | nvarchar(500), Nullable | Mô tả chi tiết về danh mục |
| `icon` | nvarchar(50), Nullable | Tên icon hoặc class icon để hiển thị |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo danh mục |

**Quan hệ**:
- Một Category có thể có nhiều Items

---

## 3. Bảng Items (Sản Phẩm)

**Mục đích**: Lưu trữ thông tin chi tiết về sản phẩm được đăng bán đấu giá.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của sản phẩm |
| `seller_id` | int (FK → Users) | ID người bán sản phẩm |
| `category_id` | int (FK → Categories) | ID danh mục sản phẩm thuộc về |
| `title` | nvarchar(255) | Tiêu đề sản phẩm |
| `description` | nvarchar(max), Nullable | Mô tả chi tiết về sản phẩm |
| `item_specifics` | nvarchar(max), Nullable | Thông số kỹ thuật chi tiết (JSON hoặc text) |
| `images` | nvarchar(max), Nullable | Danh sách URL ảnh sản phẩm (JSON array hoặc comma-separated) |
| `condition` | nvarchar(50), Nullable | Tình trạng sản phẩm (ví dụ: "Mới", "Đã qua sử dụng", "Như mới") |
| `location` | nvarchar(255), Nullable | Địa điểm/địa chỉ của sản phẩm |
| `base_price` | decimal(18,2) | Giá cơ sở/giá khởi điểm của sản phẩm |
| `status` | nvarchar(20), Default: "pending" | Trạng thái sản phẩm: "pending" (chờ duyệt), "approved" (đã duyệt), "rejected" (từ chối) |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo sản phẩm |

**Quan hệ**:
- Một Item thuộc về một Category
- Một Item thuộc về một User (Seller)
- Một Item có thể có nhiều Auctions (một sản phẩm có thể được đấu giá nhiều lần)

---

## 4. Bảng Auctions (Phiên Đấu Giá)

**Mục đích**: Lưu trữ thông tin về các phiên đấu giá đang diễn ra hoặc đã kết thúc.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của phiên đấu giá |
| `item_id` | int (FK → Items) | ID sản phẩm được đấu giá |
| `seller_id` | int (FK → Users) | ID người bán (chủ phiên đấu giá) |
| `starting_bid` | decimal(18,2) | Giá khởi điểm của phiên đấu giá |
| `current_bid` | decimal(18,2), Nullable | Giá đấu giá hiện tại (cao nhất) |
| `buy_now_price` | decimal(18,2), Nullable | Giá mua ngay (nếu có, người mua có thể mua ngay không cần đấu giá) |
| `start_time` | datetime2 | Thời điểm bắt đầu phiên đấu giá |
| `end_time` | datetime2 | Thời điểm kết thúc phiên đấu giá |
| `status` | nvarchar(20) | Trạng thái: "pending" (chờ duyệt), "active" (đang diễn ra), "paused" (tạm dừng), "ended" (đã kết thúc), "cancelled" (đã hủy) |
| `bid_count` | int, Default: 0 | Tổng số lượt đấu giá đã có |
| `winner_id` | int (FK → Users), Nullable | ID người thắng cuộc (sau khi phiên kết thúc) |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo phiên đấu giá |
| `paused_at` | datetime2, Nullable | Thời điểm tạm dừng phiên đấu giá (nếu có) |

**Quan hệ**:
- Một Auction thuộc về một Item
- Một Auction thuộc về một User (Seller)
- Một Auction có thể có một Winner (User)
- Một Auction có thể có nhiều Bids, AutoBids, Messages, Ratings, Watchlists

---

## 5. Bảng Bids (Lượt Đấu Giá)

**Mục đích**: Lưu trữ lịch sử tất cả các lượt đấu giá của người dùng.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của lượt đấu giá |
| `auction_id` | int (FK → Auctions) | ID phiên đấu giá |
| `bidder_id` | int (FK → Users) | ID người đấu giá |
| `amount` | decimal(18,2) | Số tiền đấu giá |
| `bid_time` | datetime2, Default: sysutcdatetime() | Thời điểm đấu giá |
| `is_auto_bid` | bool, Default: false | Có phải là đấu giá tự động không (true = tự động, false = thủ công) |

**Quan hệ**:
- Một Bid thuộc về một Auction
- Một Bid thuộc về một User (Bidder)

---

## 6. Bảng AutoBids (Đấu Giá Tự Động)

**Mục đích**: Lưu trữ cài đặt đấu giá tự động của người dùng cho từng phiên đấu giá.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của cài đặt đấu giá tự động |
| `auction_id` | int (FK → Auctions) | ID phiên đấu giá |
| `user_id` | int (FK → Users) | ID người dùng cài đặt đấu giá tự động |
| `max_amount` | decimal(18,2) | Số tiền tối đa mà hệ thống sẽ tự động đấu giá |
| `is_active` | bool, Default: true | Trạng thái hoạt động (true = đang bật, false = đã tắt) |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo cài đặt |

**Quan hệ**:
- Một AutoBid thuộc về một Auction
- Một AutoBid thuộc về một User
- Unique constraint: Một User chỉ có thể có một AutoBid cho một Auction

---

## 7. Bảng Watchlist (Danh Sách Theo Dõi)

**Mục đích**: Lưu trữ danh sách các phiên đấu giá mà người dùng đang theo dõi.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi theo dõi |
| `user_id` | int (FK → Users) | ID người dùng đang theo dõi |
| `auction_id` | int (FK → Auctions) | ID phiên đấu giá được theo dõi |
| `added_at` | datetime2, Default: sysutcdatetime() | Thời điểm thêm vào danh sách theo dõi |

**Quan hệ**:
- Một Watchlist thuộc về một User
- Một Watchlist thuộc về một Auction
- Unique constraint: Một User chỉ có thể theo dõi một Auction một lần

---

## 8. Bảng Messages (Tin Nhắn)

**Mục đích**: Lưu trữ tin nhắn giữa các người dùng, có thể liên quan đến một phiên đấu giá cụ thể.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của tin nhắn |
| `sender_id` | int (FK → Users) | ID người gửi tin nhắn |
| `receiver_id` | int (FK → Users) | ID người nhận tin nhắn |
| `auction_id` | int (FK → Auctions), Nullable | ID phiên đấu giá liên quan (nếu có) |
| `content` | nvarchar(max) | Nội dung tin nhắn |
| `is_read` | bool, Default: false | Trạng thái đã đọc (true = đã đọc, false = chưa đọc) |
| `sent_at` | datetime2, Default: sysutcdatetime() | Thời điểm gửi tin nhắn |

**Quan hệ**:
- Một Message có một Sender (User)
- Một Message có một Receiver (User)
- Một Message có thể liên quan đến một Auction (optional)

---

## 9. Bảng Notifications (Thông Báo)

**Mục đích**: Lưu trữ các thông báo hệ thống gửi cho người dùng.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của thông báo |
| `user_id` | int (FK → Users) | ID người dùng nhận thông báo |
| `type` | nvarchar(50), Nullable | Loại thông báo (ví dụ: "bid_outbid", "auction_ended", "order_shipped") |
| `message` | nvarchar(500) | Nội dung thông báo |
| `link` | nvarchar(500), Nullable | URL liên kết khi click vào thông báo |
| `is_read` | bool, Default: false | Trạng thái đã đọc (true = đã đọc, false = chưa đọc) |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo thông báo |

**Quan hệ**:
- Một Notification thuộc về một User

---

## 10. Bảng Ratings (Đánh Giá)

**Mục đích**: Lưu trữ đánh giá giữa người mua và người bán sau khi hoàn thành giao dịch.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của đánh giá |
| `auction_id` | int (FK → Auctions) | ID phiên đấu giá liên quan |
| `rater_id` | int (FK → Users) | ID người đánh giá |
| `rated_id` | int (FK → Users) | ID người được đánh giá |
| `rating` | int | Điểm đánh giá (thường là 1-5 sao) |
| `comment` | nvarchar(1000), Nullable | Nhận xét/bình luận kèm theo |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo đánh giá |

**Quan hệ**:
- Một Rating thuộc về một Auction
- Một Rating có một Rater (User - người đánh giá)
- Một Rating có một Rated (User - người được đánh giá)
- Unique constraint: Một Rater chỉ có thể đánh giá một Rated một lần cho một Auction

---

## 11. Bảng UserRoles (Vai Trò Người Dùng)

**Mục đích**: Lưu trữ phân quyền/vai trò của người dùng trong hệ thống (buyer, seller, admin).

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi vai trò |
| `user_id` | int (FK → Users) | ID người dùng |
| `role` | nvarchar(20) | Vai trò: "buyer", "seller", "admin" |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm gán vai trò |

**Quan hệ**:
- Một UserRole thuộc về một User
- Unique constraint: Một User chỉ có thể có một vai trò cụ thể một lần (một User có thể có nhiều vai trò khác nhau)

---

## 12. Bảng FavoriteSellers (Người Bán Yêu Thích)

**Mục đích**: Lưu trữ danh sách người bán mà người mua đã thêm vào yêu thích.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi yêu thích |
| `buyer_id` | int (FK → Users) | ID người mua |
| `seller_id` | int (FK → Users) | ID người bán được yêu thích |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm thêm vào yêu thích |

**Quan hệ**:
- Một FavoriteSeller có một Buyer (User)
- Một FavoriteSeller có một Seller (User)
- Unique constraint: Một Buyer chỉ có thể yêu thích một Seller một lần

---

## 13. Bảng EmailVerifications (Xác Thực Email)

**Mục đích**: Lưu trữ token xác thực email khi người dùng đăng ký hoặc yêu cầu xác thực lại.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi xác thực |
| `user_id` | int (FK → Users) | ID người dùng cần xác thực |
| `email` | nvarchar(255) | Email cần xác thực |
| `token` | nvarchar(255) | Token xác thực (mã duy nhất) |
| `expires_at` | datetime2 | Thời điểm hết hạn token |
| `is_used` | bool, Default: false | Đã sử dụng token chưa (true = đã dùng, false = chưa dùng) |

**Quan hệ**:
- Một EmailVerification thuộc về một User
- Cascade delete: Khi User bị xóa, EmailVerification cũng bị xóa

---

## 14. Bảng ContactMessages (Tin Nhắn Liên Hệ)

**Mục đích**: Lưu trữ tin nhắn liên hệ từ người dùng gửi cho admin/quản trị viên.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của tin nhắn liên hệ |
| `user_id` | int (FK → Users), Nullable | ID người dùng gửi (có thể null nếu người dùng chưa đăng nhập) |
| `name` | nvarchar(255) | Tên người gửi |
| `email` | nvarchar(255) | Email người gửi |
| `subject` | nvarchar(255) | Tiêu đề tin nhắn |
| `message` | nvarchar(max) | Nội dung tin nhắn |
| `status` | nvarchar(20), Default: "pending" | Trạng thái: "pending" (chờ xử lý), "replied" (đã trả lời), "closed" (đã đóng) |
| `admin_reply` | nvarchar(max), Nullable | Phản hồi từ admin |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm gửi tin nhắn |

**Quan hệ**:
- Một ContactMessage có thể thuộc về một User (optional)

---

## 15. Bảng Orders (Đơn Hàng)

**Mục đích**: Lưu trữ thông tin đơn hàng sau khi phiên đấu giá kết thúc và người thắng cuộc thanh toán.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của đơn hàng |
| `auction_id` | int (FK → Auctions) | ID phiên đấu giá liên quan |
| `buyer_id` | int (FK → Users) | ID người mua (người thắng cuộc) |
| `seller_id` | int (FK → Users) | ID người bán |
| `final_price` | decimal(18,2) | Giá cuối cùng của đơn hàng (giá đấu giá thắng cuộc) |
| `order_status` | nvarchar(50) | Trạng thái đơn hàng: "awaiting_payment" (chờ thanh toán), "awaiting_shipment" (chờ vận chuyển), "shipped" (đã gửi hàng), "dispute" (đang tranh chấp), "completed" (hoàn thành), "cancelled" (đã hủy) |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo đơn hàng |
| `updated_at` | datetime2, Nullable | Thời điểm cập nhật đơn hàng lần cuối |
| `cancelled_at` | datetime2, Nullable | Thời điểm hủy đơn hàng (nếu có) |
| `cancel_reason` | nvarchar(500), Nullable | Lý do hủy đơn hàng |
| `tracking_number` | nvarchar(255), Nullable | Mã vận đơn (tracking number) |
| `shipping_company` | nvarchar(100), Nullable | Tên công ty vận chuyển |
| `shipped_at` | datetime2, Nullable | Thời điểm gửi hàng |
| `shipping_address` | nvarchar(500), Nullable | Địa chỉ giao hàng |

**Quan hệ**:
- Một Order thuộc về một Auction
- Một Order có một Buyer (User)
- Một Order có một Seller (User)
- Một Order có thể có một Payment (1-1)
- Một Order có thể có một Dispute (1-1)

---

## 16. Bảng Payments (Thanh Toán)

**Mục đích**: Lưu trữ thông tin thanh toán cho các đơn hàng.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của thanh toán |
| `order_id` | int (FK → Orders), Unique | ID đơn hàng (một đơn hàng chỉ có một thanh toán) |
| `amount` | decimal(18,2) | Số tiền thanh toán |
| `payment_status` | nvarchar(50) | Trạng thái thanh toán: "pending" (chờ thanh toán), "paid_held" (đã thanh toán - đang giữ), "hold_dispute" (giữ do tranh chấp), "refunded_to_buyer" (đã hoàn tiền cho người mua), "released_to_seller" (đã giải phóng cho người bán) |
| `payment_method` | nvarchar(50), Nullable | Phương thức thanh toán: "credit_card", "bank_transfer", "e_wallet", v.v. |
| `transaction_id` | nvarchar(255), Nullable | ID giao dịch từ nhà cung cấp thanh toán |
| `payment_provider` | nvarchar(100), Nullable | Nhà cung cấp thanh toán: "stripe", "paypal", "vnpay", v.v. |
| `paid_at` | datetime2, Nullable | Thời điểm thanh toán |
| `released_at` | datetime2, Nullable | Thời điểm giải phóng tiền cho người bán |
| `refunded_at` | datetime2, Nullable | Thời điểm hoàn tiền cho người mua |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo bản ghi thanh toán |
| `updated_at` | datetime2, Nullable | Thời điểm cập nhật lần cuối |
| `notes` | nvarchar(1000), Nullable | Ghi chú về thanh toán |

**Quan hệ**:
- Một Payment thuộc về một Order (1-1)
- Cascade delete: Khi Order bị xóa, Payment cũng bị xóa

---

## 17. Bảng Disputes (Tranh Chấp)

**Mục đích**: Lưu trữ thông tin các tranh chấp giữa người mua và người bán về đơn hàng.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của tranh chấp |
| `order_id` | int (FK → Orders), Unique | ID đơn hàng (một đơn hàng chỉ có một tranh chấp) |
| `buyer_id` | int (FK → Users) | ID người mua (người khiếu nại) |
| `seller_id` | int (FK → Users) | ID người bán (người bị khiếu nại) |
| `reason` | nvarchar(500) | Lý do tranh chấp |
| `description` | nvarchar(max), Nullable | Mô tả chi tiết về tranh chấp |
| `status` | nvarchar(50) | Trạng thái: "pending" (chờ xử lý), "in_review" (đang xem xét), "buyer_won" (người mua thắng), "seller_won" (người bán thắng), "resolved" (đã giải quyết), "closed" (đã đóng) |
| `resolution` | nvarchar(50), Nullable | Giải pháp: "refunded_to_buyer" (hoàn tiền cho người mua), "released_to_seller" (giải phóng cho người bán) |
| `resolved_by` | int (FK → Users), Nullable | ID admin/quản trị viên giải quyết tranh chấp |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tạo tranh chấp |
| `resolved_at` | datetime2, Nullable | Thời điểm giải quyết tranh chấp |
| `closed_at` | datetime2, Nullable | Thời điểm đóng tranh chấp |
| `admin_notes` | nvarchar(max), Nullable | Ghi chú từ admin về tranh chấp |

**Quan hệ**:
- Một Dispute thuộc về một Order (1-1)
- Một Dispute có một Buyer (User)
- Một Dispute có một Seller (User)
- Một Dispute có thể có một Resolver (User - admin)
- Cascade delete: Khi Order bị xóa, Dispute cũng bị xóa

---

## 18. Bảng AuctionHistory (Lịch Sử Đấu Giá)

**Mục đích**: Lưu trữ lịch sử các phiên đấu giá đã kết thúc để phân tích và báo cáo.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi lịch sử |
| `auction_id` | int | ID phiên đấu giá gốc (có thể không còn tồn tại) |
| `item_id` | int | ID sản phẩm |
| `title` | nvarchar(255) | Tiêu đề sản phẩm tại thời điểm đấu giá |
| `category_id` | int | ID danh mục |
| `seller_id` | int | ID người bán |
| `winner_id` | int, Nullable | ID người thắng cuộc |
| `starting_bid` | decimal(18,2) | Giá khởi điểm |
| `final_bid` | decimal(18,2), Nullable | Giá cuối cùng (giá thắng cuộc) |
| `total_bids` | int, Default: 0 | Tổng số lượt đấu giá |
| `start_time` | datetime2 | Thời điểm bắt đầu |
| `end_time` | datetime2 | Thời điểm kết thúc |
| `completed_at` | datetime2, Default: sysutcdatetime() | Thời điểm hoàn thành (khi được ghi vào lịch sử) |

**Quan hệ**:
- Bảng này là snapshot/history table, không có foreign key constraints để tránh lỗi khi dữ liệu gốc bị xóa

---

## 19. Bảng SearchKeywords (Từ Khóa Tìm Kiếm)

**Mục đích**: Lưu trữ lịch sử tìm kiếm của người dùng để phân tích và đề xuất.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi tìm kiếm |
| `user_id` | int (FK → Users) | ID người dùng thực hiện tìm kiếm |
| `keyword` | nvarchar(255) | Từ khóa tìm kiếm |
| `created_at` | datetime2, Default: sysutcdatetime() | Thời điểm tìm kiếm |

**Quan hệ**:
- Một SearchKeyword thuộc về một User
- Cascade delete: Khi User bị xóa, SearchKeywords cũng bị xóa

---

## 20. Bảng UserAuctionViews (Lượt Xem Phiên Đấu Giá)

**Mục đích**: Lưu trữ lịch sử lượt xem của người dùng đối với các phiên đấu giá để phân tích và đề xuất.

| Trường | Kiểu Dữ Liệu | Mô Tả |
|--------|--------------|-------|
| `id` | int (PK, Identity) | ID duy nhất của bản ghi lượt xem |
| `user_id` | int (FK → Users) | ID người dùng xem |
| `auction_id` | int (FK → Auctions) | ID phiên đấu giá được xem |
| `viewed_at` | datetime2(7) | Thời điểm xem (độ chính xác đến millisecond) |

**Quan hệ**:
- Một UserAuctionView thuộc về một User
- Một UserAuctionView thuộc về một Auction
- Cascade delete: Khi Auction bị xóa, UserAuctionViews cũng bị xóa

---

## Sơ Đồ Quan Hệ Chính

### Quan hệ 1-Nhiều (One-to-Many):
- **Users** → Auctions (Seller), Auctions (Winner), Bids, AutoBids, Items, Messages, Notifications, Ratings, Watchlists, Orders, Disputes, UserRoles, FavoriteSellers, SearchKeywords, UserAuctionViews
- **Categories** → Items
- **Items** → Auctions
- **Auctions** → Bids, AutoBids, Messages, Ratings, Watchlists, Orders, UserAuctionViews

### Quan hệ 1-1 (One-to-One):
- **Orders** ↔ Payments
- **Orders** ↔ Disputes

### Quan hệ Nhiều-Nhiều (Many-to-Many):
- **Users ↔ Users** (qua FavoriteSellers: Buyer-Seller)
- **Users ↔ Auctions** (qua Watchlist: User theo dõi Auction)
- **Users ↔ Auctions** (qua Bids: User đấu giá Auction)

---

## Ghi Chú Quan Trọng

1. **Trạng thái (Status)**: Nhiều bảng sử dụng trường `status` với các giá trị enum cụ thể. Cần đảm bảo tính nhất quán khi cập nhật.

2. **Cascade Delete**: Một số quan hệ có cascade delete để đảm bảo tính toàn vẹn dữ liệu:
   - EmailVerification → User
   - SearchKeyword → User
   - Payment → Order
   - Dispute → Order
   - UserAuctionView → Auction

3. **Unique Constraints**: Một số bảng có unique constraints để tránh dữ liệu trùng lặp:
   - Users.email
   - Categories.slug
   - AutoBids (auction_id, user_id)
   - Watchlist (user_id, auction_id)
   - FavoriteSellers (buyer_id, seller_id)
   - UserRoles (user_id, role)
   - Ratings (auction_id, rater_id, rated_id)
   - Payments.order_id (1-1 với Orders)
   - Disputes.order_id (1-1 với Orders)

4. **Indexes**: Các bảng có indexes trên các trường thường được query để tối ưu hiệu suất:
   - Status fields
   - Foreign keys
   - Email, Slug
   - Composite indexes cho unique constraints

5. **Default Values**: Nhiều trường có giá trị mặc định để đảm bảo dữ liệu hợp lệ ngay khi tạo mới.

---

*Tài liệu này được tạo tự động từ các model definitions trong codebase. Cập nhật lần cuối: 2024*

