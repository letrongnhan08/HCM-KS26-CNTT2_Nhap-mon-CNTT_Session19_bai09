Bài 9: Thiết Kế Kiến Trúc Két Sắt Số Bitwarden Và Chính Sách Mật Khẩu Game Studio

# Phần 1:

**Phân tích vì sao thói quen lưu mật khẩu trên trình duyệt Google Chrome lại nguy hiểm:**

* Do không thiết lập mật khẩu (Master Password) khiến máy bị hack Infostealer
* Cơ chế lưu trữ kém bảo mật cục bộ trên Chrome vì Chrome lưu trữ tên đăng nhập và mật khẩu trên máy tính ví dụ window (%LOCALAPPDATA%\\Google\\Chrome\\User Data\\Default\\LoginData) và nếu Infostealer xâm nhập thì chỉ cần đọc trực tiếp tệp Login Data và tìm hàm API để giải mã toàn bộ mật khẩu về dạng văn bản (Plaintext).

**Giải thích bản chất kỹ thuật của Kiến trúc Zero-Knowledge và cơ chế Client-side Decryption:**

* Zero-Knowledge (Không-kiến-thức):  Là mô hình thiết kế hệ thống mà dịch vụ quản lý mật khẩu như Bitwarden, hoàn toàn không biết được (Master Password) của người dùng
Cơ chế : Mật khẩu không truyền tải dưới dạng (plaintext) qua internet và server không lưu mật khẩu thô.server chỉ xác minh xem mật khẩu người dùng có đúng không
* Client-side Decryption (Mã hóa client):Được mã hóa bằng chuẩn AES-256 bit ngay trên thiết bị của người dùng với bảo mật cao
Cơ chế:mã hóa từ chính (master password) của người dùng kết hợp 1 chuỗi ký tự ngẫu nhiên và ký tự ngẫu nhiên chỉ tồn tại trong bộ nhớ tạm của thiết bị người dùng (client), không tồn tại trên server.

**Nếu máy chủ đám mây của Bitwarden bị hacker tấn công,dữ liệu PixelForge Studio ra sao:
-Dữ liệu PixelForge Studio vẫn an toàn không bị mã độc tấn công vì:**

* Hacker chỉ lấy được Ciphertext trên Bitwarden vì được mã hóa AES-256 bit thì chỉ toàn là ký tự ngẫu nhiên ở trên server, không phải Master Password của PixelForge Studio vì mật khẩu thật luôn nằm trong thiết bị của PixelForge
* mã hàm mật khẩu có tính bảo mật cao 16-32 ký tự ngẫu nhiên có thể thay đổi hàng trăm nghìn vòng lặp ký tự ngẫu nhiên chặn các cuộc tấn công như Brute-force Attack ,hacker có thể mất hàng triệu năm thì mới tìm được mật khẩu đúng

# Phần 2:

**Phương pháp tạo Master Passphrase đạt chuẩn:**
sử dụng Master Passphrase dễ nhớ ,trừu phú không logic,có ký tự đặc biệt
Ví dụ:
Nhat-tien#04-van-hog@jOy

**Xây dựng bảng cấu hình chuẩn cho công cụ Generator trong Bitwarden**

| cấu hình bảo mật mật khẩu | Giá trị chuẩn cần có | áp dụng                                                                          |
| ------------------------- | -------------------- | -------------------------------------------------------------------------------- |
| Quy định độ dài           | 16-32 ký tự          | làm cho Hacker không thể dò đúng mật khẩu                                        |
| loại ký tự hoa            | từ A-Z               | Tăng độ bảo mật cho mật khẩu có mẫu ký tự hoa A-Z thay vì mỗi chữ thường a-z     |
| chữ thường                | từ a-z               | Tăng độ bảo mật                                                                  |
| chữ số                    | từ 0-9               | Tăng độ bảo mật,đưa số vào ký tự                                                 |
| Ký tự đặc biệt            | !@#$%^\\\&\\\*       | tăng độ bảo mật, phức tạp làm các cuộc  tấn công của Hacker không thể thành công |


**Giải thích tính năng Tự động Điền (Auto-fill) của Bitwarden hiệu quả hơn con người**
Auto-fill sẽ chặn không cho vào các tên miền lạ như githúb.com hay eplc.xyz bắt buộc khớp tên miền chính xác (Domain Matching) 100%.
Do con người se dễ bị lừa trước các trang web giả mạo quá giống web chính thống như logo,giao diện,tên miền (ví dụ chữ là github.com nhưng khác định dạng phông chữ) thì dẫn đến bị lừa khác máy móc tính toán.

**Thiết lập quy trình lưu trữ Khóa Cấp cứu (Emergency Kit)**
Quy trình chuẩn 4 bước:
Bước 1 (Yêu cầu): Trưởng nhóm gửi yêu cầu quyền truy cập khẩn cấp (Take Over ,View Vault) trực tiếp qua giao diện quản trị Bitwarden ,chỉ có trưởng nhóm mới có quyền ủy quyền
Bước 2 (Chờ xác thực):Đặt thời gian chờ 24 giờ ,Khi xảy ra sự cố, trưởng nhóm chỉ cần gửi yêu cầu quyền truy cập khẩn cấp và chờ email hệ thống gửi , nếu 24 giờ trôi qua không có phản hồi từ chối hệ thống sẽ tự động cấp quyền cho trưởng nhóm
Bước 3 (khôi phục): cấp phát lại quyền truy cập cho đội ngũ để tiếp tục dự án công việc.
