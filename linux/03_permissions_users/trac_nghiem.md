# Trắc nghiệm — Linux Permissions & Users

> **Tổng số câu:** 20 | **Cơ bản (30%) · Trung bình (40%) · Nâng cao (30%)**

---

## Phần 1 — Cơ bản (câu 1–6)

**Câu 1:** Permission `-rwxr-xr--` chuyển sang dạng số là gì?

- A. 777
- B. 754
- C. 644
- D. 755

> **Đáp án: B** — Owner: rwx=7, Group: r-x=5, Others: r--=4 → 754. Nhớ: r=4, w=2, x=1.

---

**Câu 2:** Lệnh nào cho file `script.sh` quyền execute?

- A. `chmod 644 script.sh`
- B. `chmod +x script.sh`
- C. `chmod -x script.sh`
- D. `chmod w+x script.sh`

> **Đáp án: B** — `+x` thêm execute bit cho tất cả (owner, group, others). `chmod 644` là rw-r--r-- (không có execute). `-x` bỏ execute.

---

**Câu 3:** Lệnh `whoami` làm gì?

- A. Hiển thị tất cả users trong hệ thống
- B. Hiển thị tên của user đang đăng nhập hiện tại
- C. Hiển thị thông tin chi tiết về user
- D. Đổi tên user

> **Đáp án: B** — `whoami` in ra username hiện tại. `id` hiện thêm UID, GID, groups. `who` liệt kê tất cả users đang login.

---

**Câu 4:** File `/home/alice` thuộc về alice, permission là `-rw-------`. Bob có thể đọc file này không?

- A. Có, nếu bob ở cùng group
- B. Không, vì others không có quyền đọc
- C. Có, nếu bob là admin
- D. Có, mọi user đều có thể đọc file trong /home

> **Đáp án: B** — `rw-------` = owner có rw, group có nothing (---), others có nothing (---). Bob (others) không có read permission. Chỉ alice (owner) và root mới đọc được.

---

**Câu 5:** Lệnh nào thêm user `alice` vào group `docker`?

- A. `usermod -G docker alice`
- B. `usermod -aG docker alice`
- C. `groupadd alice docker`
- D. `useradd -G docker alice`

> **Đáp án: B** — **BẮT BUỘC** dùng `-a` (append) với `-G`. Thiếu `-a` → `usermod -G docker alice` sẽ xóa alice khỏi tất cả groups khác và chỉ còn trong docker!

---

**Câu 6:** `sudo` cho phép làm gì?

- A. Switch sang user khác
- B. Chạy lệnh với quyền của user khác (thường là root)
- C. Tạo user mới
- D. Xem tất cả commands đã chạy

> **Đáp án: B** — `sudo command` chạy command với quyền root (hoặc user khác nếu chỉ định). User phải có trong `/etc/sudoers` để dùng sudo.

---

## Phần 2 — Trung bình (câu 7–14)

**Câu 7:** Permission 644 cho file và 755 cho thư mục — tại sao thư mục cần execute bit?

- A. Execute bit trên thư mục cho phép chạy các file trong đó
- B. Execute bit trên thư mục cho phép `cd` vào thư mục và list nội dung
- C. Execute bit không cần thiết, 644 cũng được cho thư mục
- D. Execute bit mã hóa thư mục

> **Đáp án: B** — Với thư mục: `r` = đọc danh sách file (ls), `w` = tạo/xóa file trong thư mục, `x` = traverse (cd vào, access files). Không có `x` = không thể `cd` vào thư mục dù có `r`.

---

**Câu 8:** `umask 022` có tác động thế nào đến file mới tạo?

- A. File mới có permission 022
- B. File mới có permission 644 (= 666 - 022)
- C. File mới có permission 755
- D. File mới không có permission nào

> **Đáp án: B** — umask "che" đi bits: file mặc định = 666, trừ umask 022 = 644 (rw-r--r--). Directory mặc định = 777, trừ umask 022 = 755 (rwxr-xr-x).

---

**Câu 9:** SUID bit trên executable file có tác dụng gì?

- A. File chỉ owner mới chạy được
- B. Khi user chạy file, process chạy với quyền của owner (thường root) thay vì user
- C. File được chia sẻ giữa nhiều users
- D. File có thể chạy tự động khi boot

> **Đáp án: B** — SUID (Set User ID): ví dụ `/usr/bin/passwd` có SUID root. Khi alice chạy passwd, process có quyền root để sửa `/etc/shadow`. Hiển thị: `rws` (SUID) thay vì `rwx`.

---

**Câu 10:** Sticky bit trên thư mục `/tmp` có tác dụng gì?

- A. Thư mục không thể xóa
- B. Chỉ owner của file mới xóa được file của mình, dù others có write trên thư mục
- C. Files trong thư mục tự xóa sau một thời gian
- D. Thư mục được cache trong RAM

> **Đáp án: B** — Sticky bit: trong `/tmp` (permission 1777 = drwxrwxrwt), mọi user có thể tạo file, nhưng chỉ owner của file (hoặc root) mới xóa được. Ngăn user xóa file của người khác.

---

**Câu 11:** Lệnh `chmod -R 755 directory/` làm gì?

- A. Đổi permission của chỉ thư mục gốc
- B. Đổi permission của thư mục và tất cả file/thư mục bên trong thành 755
- C. Đổi ownership
- D. Xóa các file không có permission 755

> **Đáp án: B** — `-R` recursive. Vấn đề: 755 áp lên cả file (thường file nên là 644, không cần execute). Best practice: dùng `find` để chỉ set dirs=755 và files=644 riêng.

---

**Câu 12:** `getent passwd alice` lấy thông tin gì?

- A. Password của alice
- B. Entry của alice trong /etc/passwd kể cả từ LDAP/NIS
- C. Group của alice
- D. Login history của alice

> **Đáp án: B** — `getent` (get entries) tra cứu từ NSS (Name Service Switch), bao gồm cả LDAP, NIS, không chỉ local files. Output: `alice:x:1000:1000:Alice Smith:/home/alice:/bin/bash`.

---

**Câu 13:** Tại sao SSH private key phải có permission 600?

- A. Vì SSH server yêu cầu
- B. SSH client từ chối dùng key nếu permission không đủ strict — nguy cơ key bị đọc bởi user khác
- C. 600 tăng tốc độ SSH
- D. Không cần thiết, SSH vẫn hoạt động với 644

> **Đáp án: B** — OpenSSH kiểm tra permission của private key. Nếu group hoặc others có read (`644`), SSH sẽ từ chối với lỗi "WARNING: UNPROTECTED PRIVATE KEY FILE!". 600 = chỉ owner đọc được.

---

**Câu 14:** `sudo -l` làm gì?

- A. List tất cả users có thể dùng sudo
- B. Liệt kê các quyền sudo của user hiện tại
- C. Logout sau khi dùng sudo
- D. Lock sudo access

> **Đáp án: B** — `sudo -l` in ra những lệnh nào user được phép chạy với sudo (từ `/etc/sudoers`). Hữu ích để kiểm tra quyền trước khi làm gì đó.

---

## Phần 3 — Nâng cao (câu 15–20)

**Câu 15:** ACL khác permission truyền thống (owner/group/others) như thế nào?

- A. ACL nhanh hơn permission thường
- B. ACL cho phép set quyền cho nhiều user/group cụ thể, không bị giới hạn 3 loại (owner/group/others)
- C. ACL chỉ dùng được với root
- D. Không có sự khác biệt thực tế

> **Đáp án: B** — Unix permission giới hạn: 1 owner, 1 group, others. Với ACL: `setfacl -m u:bob:rw file` → bob có rw dù bob không phải owner và không trong group. `getfacl file` xem ACL hiện tại. File có ACL sẽ thấy `+` trong `ls -la`.

---

**Câu 16:** Lệnh `chmod 4755 /usr/bin/program` set gì?

- A. SGID + rwxr-xr-x
- B. SUID + rwxr-xr-x (owner là root → chạy với quyền root)
- C. Sticky bit + rwxr-xr-x
- D. SUID + rwxr-x--- 

> **Đáp án: B** — Chữ số đầu là special bits: 4=SUID, 2=SGID, 1=sticky. `4755`: SUID + owner rwx (7) + group rx (5) + others rx (5). `ls` hiện: `-rwsr-xr-x`.

---

**Câu 17:** User thường (non-root) có thể chạy lệnh nào thành công?

- A. `chown root file.txt`
- B. `chmod 755 /etc/passwd`
- C. `chmod 644 ~/myfile.txt`
- D. `useradd newuser`

> **Đáp án: C** — User chỉ có thể `chmod` file mà họ là owner. `chown` chỉ root mới chạy được. `chmod /etc/passwd` cần root vì file thuộc về root. `useradd` cần root.

---

**Câu 18:** `/etc/sudoers` có dòng `alice ALL=(ALL) NOPASSWD: /usr/bin/apt`. Alice có thể làm gì?

- A. Chạy mọi lệnh không cần password
- B. Chỉ chạy `/usr/bin/apt` với quyền root, không cần nhập password
- C. Chạy apt với bất kỳ user nào không cần password
- D. Chạy apt chỉ trên localhost

> **Đáp án: B** — `NOPASSWD: /usr/bin/apt` = chỉ cho phép `sudo apt` mà không cần password. `ALL=(ALL) NOPASSWD: ALL` mới là mọi lệnh không cần password.

---

**Câu 19:** Sau khi chạy `usermod -aG docker alice`, alice phải làm gì để quyền docker có hiệu lực?

- A. Không cần làm gì, có hiệu lực ngay
- B. Logout và login lại để shell reload group membership
- C. Chạy `sudo systemctl restart docker`
- D. Chạy `chmod +x /var/run/docker.sock`

> **Đáp án: B** — Group membership được load khi login. Session đang chạy không tự cập nhật. Sau logout/login mới thấy docker trong `groups`. Workaround không cần logout: `newgrp docker` (tạo subshell với group mới).

---

**Câu 20:** Phân tích: file có permission `drwsr-sr-x`. Giải thích SGID trên directory này?

- A. Chỉ owner mới vào được thư mục
- B. File mới tạo trong thư mục tự động inherit group của thư mục, không phải primary group của user tạo
- C. Thư mục không thể xóa
- D. Execute script trong thư mục với quyền group

> **Đáp án: B** — SGID trên directory (`chmod 2755 dir`): khi user alice (group alice) tạo file trong thư mục thuộc group `developers`, file mới sẽ có group `developers` (không phải `alice`). Hữu ích cho shared project directories.

---

## Bảng đáp án nhanh

| Câu | Đáp án | Câu | Đáp án |
|-----|--------|-----|--------|
| 1   | B      | 11  | B      |
| 2   | B      | 12  | B      |
| 3   | B      | 13  | B      |
| 4   | B      | 14  | B      |
| 5   | B      | 15  | B      |
| 6   | B      | 16  | B      |
| 7   | B      | 17  | C      |
| 8   | B      | 18  | B      |
| 9   | B      | 19  | B      |
| 10  | B      | 20  | B      |
