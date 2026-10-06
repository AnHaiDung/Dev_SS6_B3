# Báo cáo Bài 3: Cấu hình Tường lửa UFW và Chẩn đoán Cổng Mạng

## 1. Thông tin chung

* **Bài tập:** Bài 3 - Cấu hình tường lửa UFW và chẩn đoán cổng mạng
* **Đường dẫn nộp bài:** `homework/session_06/ex3/README.md`
* **Mục tiêu:** Sử dụng UFW để bảo vệ máy chủ Cloud VPS, cấu hình chính sách mặc định, mở các cổng mạng SSH (22) & Web App (8080/tcp), và thực hiện kiểm tra chẩn đoán cổng kết nối.

---

## 2. Nhật ký thực thi các lệnh (Command Logs)

### Bước 1: Thiết lập chính sách mặc định của UFW

Cấu hình chặn toàn bộ kết nối đi vào (incoming) và cho phép toàn bộ kết nối đi ra (outgoing):

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### Bước 2: Thêm quy tắc cho phép các cổng kết nối cần thiết

Thêm quy tắc cho phép cổng SSH (22/tcp) để đảm bảo không bị ngắt kết nối quản trị và cổng ứng dụng Web (8080/tcp):

```bash
sudo ufw allow 22/tcp
sudo ufw allow 8080/tcp
```

### Bước 3: Kích hoạt tường lửa UFW

Bật UFW và xác nhận kích hoạt (`y`):

```bash
sudo ufw enable
```

---

## 3. Kiểm tra kết quả thực thi (Verification)

### 3.1. Xem trạng thái tường lửa chi tiết (`sudo ufw status verbose`)

Thực hiện kiểm tra trạng thái hoạt động và các quy tắc tường lửa:

```bash
sudo ufw status verbose
```

**Đầu ra của lệnh (Output):**

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
8080/tcp                   ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
8080/tcp (v6)              ALLOW IN    Anywhere (v6)             
```

### 3.2. Kiểm tra các cổng đang lắng nghe thực tế (`ss -tlnp`)

Thực hiện kiểm tra các socket TCP đang mở trên hệ thống:

```bash
ss -tlnp
```

**Đầu ra của lệnh (Output):**

```text
State      Recv-Q Send-Q Local Address:Port               Peer Address:Port              Process                                                                         
LISTEN     0      128          0.0.0.0:22                      0.0.0.0:*                  users:(("sshd",pid=842,fd=3))                                                   
LISTEN     0      128          0.0.0.0:8080                    0.0.0.0:*                  users:(("node",pid=1234,fd=18))                                                 
LISTEN     0      128             [::]:22                         [::]:*                  users:(("sshd",pid=842,fd=4))                                                   
LISTEN     0      128             [::]:8080                       [::]:*                  users:(("node",pid=1234,fd=19))                                                 
```

---

## 4. Đánh giá và Đối chiếu yêu cầu

| Tiêu chí | Trạng thái | Chi tiết đối chiếu |
| :--- | :---: | :--- |
| Trạng thái UFW |  Đạt | Status hiển thị `active` |
| Chính sách mặc định |  Đạt | `deny (incoming)`, `allow (outgoing)` |
| Quyền cổng SSH (22/tcp) |  Đạt | `22/tcp ALLOW IN Anywhere` |
| Quyền cổng Web App (8080/tcp) |  Đạt | `8080/tcp ALLOW IN Anywhere` |
| Kiểm tra cổng socket (`ss -tlnp`) |  Đạt | Đã xác nhận cổng 22 và 8080 đang trong trạng thái LISTEN |