# Linux Permissions & Users — Quyền hạn và Người dùng

---

## Giải thích cho người mới hoàn toàn

Hãy tưởng tượng một tòa chung cư. Mỗi căn hộ (file) có chủ (owner), thuộc một tầng/khu vực (group), và có khách bên ngoài (others). Chủ căn hộ quyết định: chủ nhà (owner) có thể làm gì, hàng xóm cùng tầng (group) có thể làm gì, và người ngoài đường (others) có thể làm gì.

Ba quyền cơ bản: **r** = đọc (read — nhìn vào bên trong), **w** = ghi (write — sửa đổi), **x** = thực thi (execute — với file: chạy như chương trình; với thư mục: bước vào được).

Số `755` trông có vẻ bí ẩn nhưng thực ra là chủ (7=rwx), nhóm (5=r-x), người ngoài (5=r-x). Phần sau sẽ giải thích cách tính.

`sudo` giống như mượn chìa khóa tổng của bảo vệ (root) để làm một việc cụ thể, rồi trả lại ngay. `su` giống như bảo vệ cho bạn mặc quần áo bảo vệ và đi làm thay họ.

---

## Giải thích cho người đã biết lập trình (nâng cao)

**Permission bits trong inode:** 12 bits: 3 special (setuid, setgid, sticky) + 3x3 permission bits. Kernel kiểm tra theo thứ tự: nếu là owner → dùng owner bits; else nếu trong group → dùng group bits; else dùng other bits. **Lưu ý:** nếu owner bits không cho phép nhưng other bits cho phép, owner VẪN bị từ chối.

**setuid bit (SUID):** Khi file executable có setuid, process chạy với UID của owner file, không phải user chạy. `passwd` cần ghi vào `/etc/shadow` (thuộc root) — đó là lý do nó có SUID root. `ls -la /usr/bin/passwd` → `-rwsr-xr-x` (s thay vì x trong owner execute).

**setgid bit (SGID):** Trên file: process chạy với GID của group file. Trên thư mục: file mới tạo trong thư mục đó inherit group của thư mục, thay vì primary group của user tạo. Hữu ích cho shared directories trong team.

**Sticky bit:** Trên thư mục: chỉ owner của file hoặc root mới xóa/rename file đó, dù thư mục có write permission cho all. `ls -la /tmp` → `drwxrwxrwt` (t = sticky bit). Ngăn user xóa file của nhau trong `/tmp`.

**umask và permission calculation:** Default permissions khi tạo file = 666 (rw-rw-rw-) XOR umask. Default khi tạo dir = 777 XOR umask. Umask 022 → file = 644, dir = 755. Umask là inherited từ parent process (shell).

**`/etc/shadow`:** Mật khẩu không lưu plaintext hay MD5 trong `/etc/passwd` nữa. `/etc/shadow` chứa hashed password (thường bcrypt/SHA-512), ngày đổi mật khẩu, min/max age, expiry. Chỉ root và group shadow đọc được.

---

## Permission Bits — Bảng Octal

| Octal | Binary | Ký hiệu | Ý nghĩa |
|-------|--------|---------|---------|
| 7 | 111 | rwx | Đọc + Ghi + Thực thi |
| 6 | 110 | rw- | Đọc + Ghi |
| 5 | 101 | r-x | Đọc + Thực thi |
| 4 | 100 | r-- | Chỉ Đọc |
| 3 | 011 | -wx | Ghi + Thực thi |
| 2 | 010 | -w- | Chỉ Ghi |
| 1 | 001 | --x | Chỉ Thực thi |
| 0 | 000 | --- | Không quyền gì |

**Permission format:** `-rwxr-xr-x`
- Ký tự 1: loại file (`-`=file, `d`=dir, `l`=symlink, `c`=char device, `b`=block device)
- Ký tự 2-4: owner permissions (rwx)
- Ký tự 5-7: group permissions (r-x)
- Ký tự 8-10: others permissions (r-x)

---

## Các lệnh / Cú pháp chính

| Lệnh | Mô tả | Ví dụ |
|------|-------|-------|
| `chmod 755 file` | Set permissions bằng octal | `chmod 755 script.sh` |
| `chmod +x file` | Thêm execute cho tất cả | `chmod +x deploy.sh` |
| `chmod u+w file` | Thêm write cho owner | `chmod u+w config.txt` |
| `chmod g-r file` | Bỏ read của group | `chmod g-r secret.txt` |
| `chmod o=r file` | Set others chỉ đọc | `chmod o=r report.pdf` |
| `chmod -R 755 dir/` | Đệ quy toàn thư mục | `chmod -R 755 /var/www/` |
| `chown user file` | Đổi owner | `chown www-data index.html` |
| `chown user:group file` | Đổi owner và group | `chown nginx:nginx /etc/nginx/` |
| `chown -R user:group dir/` | Đệ quy | `chown -R deploy:deploy /app/` |
| `chgrp group file` | Chỉ đổi group | `chgrp developers project/` |
| `umask` | Xem umask hiện tại | `umask` |
| `umask 027` | Set umask | `umask 027` |
| `useradd username` | Tạo user mới | `useradd -m -s /bin/bash alice` |
| `usermod -aG group user` | Thêm user vào group | `usermod -aG sudo alice` |
| `userdel -r user` | Xóa user và home | `userdel -r olduser` |
| `passwd user` | Đổi password | `passwd alice` |
| `groupadd group` | Tạo group | `groupadd developers` |
| `id user` | Xem UID/GID/groups | `id alice` |
| `groups user` | Xem groups của user | `groups alice` |
| `who` | User đang login | `who` |
| `w` | User đang làm gì | `w` |
| `getfacl file` | Xem ACL | `getfacl /var/www/html` |
| `setfacl -m u:alice:rw file` | Set ACL | `setfacl -m u:bob:r-- /shared/` |

---

## Ví dụ thực tế

```bash
# ===== Đọc permissions =====
ls -la /etc/passwd
# -rw-r--r-- 1 root root 2847 Jan 10 08:00 /etc/passwd
# ^^^^^^^^^^^
# -          = regular file
# rw-        = owner (root): read+write
# r--        = group (root): read only
# r--        = others: read only

# ===== chmod — Absolute (octal) =====
chmod 755 script.sh    # rwxr-xr-x — executable script
chmod 644 config.txt   # rw-r--r-- — readable config
chmod 600 ~/.ssh/id_rsa  # rw------- — private key (BẮT BUỘC)
chmod 700 ~/.ssh/       # rwx------ — SSH directory
chmod 777 /tmp/shared   # KHÔNG KHUYẾN KHÍCH — ai cũng làm được gì

# ===== chmod — Symbolic =====
chmod +x script.sh           # thêm execute cho owner+group+others
chmod u+x,g-w,o-rwx file     # nhiều thay đổi cùng lúc
chmod a=rw file              # set tất cả thành rw-rw-rw-
chmod u=rwx,go=rx dir/       # dir: owner full, group/others r-x

# ===== Recursive — chú ý =====
# Thường cần permissions khác nhau cho file và dir
chmod -R 755 /var/www/html   # file cũng thành 755 (có x không cần thiết)
# Cách đúng:
find /var/www/html -type f -exec chmod 644 {} \;
find /var/www/html -type d -exec chmod 755 {} \;

# ===== Special bits =====
# setuid (4): process chạy với UID của owner
chmod 4755 /usr/local/bin/myapp   # rwsr-xr-x
chmod u+s  /usr/local/bin/myapp   # cách symbolic

# setgid (2): trên thư mục, file mới inherit group
chmod 2775 /shared/team/          # rwxrwsr-x
chmod g+s  /shared/team/

# sticky bit (1): chỉ owner mới xóa file của mình
chmod 1777 /tmp/                  # rwxrwxrwt
chmod +t   /shared/uploads/       # cách symbolic

# ===== chown =====
chown alice report.pdf             # đổi owner thành alice
chown alice:developers project/    # đổi cả owner và group
chown -R www-data:www-data /var/www/
chown :nginx /etc/nginx/nginx.conf # chỉ đổi group

# ===== umask =====
umask          # 0022 — default thường gặp
umask 027      # file mới: 640 (rw-r-----), dir mới: 750 (rwxr-x---)
umask 077      # file mới: 600, dir mới: 700 — private
# Tính: file default 666 - umask 022 = 644
#       dir  default 777 - umask 022 = 755

# ===== User management =====
# Tạo user với home directory và bash shell
useradd -m -s /bin/bash -G developers alice
passwd alice          # set password

# Xem thông tin user
id alice              # uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo)
cat /etc/passwd | grep alice   # alice:x:1001:1001::/home/alice:/bin/bash

# Thêm user vào group sudo (KHÔNG dùng -G thay vì -aG!)
usermod -aG sudo alice    # -a = append, không overwrite groups

# Đổi shell
usermod -s /bin/zsh alice
# Khóa/mở tài khoản
usermod -L alice          # lock (thêm ! vào đầu hash)
usermod -U alice          # unlock

# ===== Groups =====
groupadd developers
groupdel oldteam
cat /etc/group | grep developers
# developers:x:1010:alice,bob,charlie

# ===== sudo vs su =====
sudo apt update           # chạy lệnh với quyền root
sudo -u postgres psql     # chạy với quyền user khác
sudo -i                   # mở interactive root shell
sudo -l                   # xem quyền sudo của mình
su -                      # switch sang root (cần root password)
su - alice                # switch sang alice

# ===== /etc/sudoers — cấu hình quyền sudo =====
# KHÔNG edit trực tiếp! Dùng visudo
sudo visudo
# Cú pháp: user  host=(runas)  command
# alice   ALL=(ALL) NOPASSWD: /usr/bin/apt, /usr/bin/systemctl
# %developers ALL=(ALL) /usr/bin/git

# ===== ACL — khi permission cơ bản không đủ =====
# Cho alice đọc file mà không thay đổi group
setfacl -m u:alice:r-- /etc/app.conf
# Cho nhóm developers write vào thư mục
setfacl -m g:developers:rwx /shared/code/
# Default ACL — kế thừa cho file mới trong thư mục
setfacl -d -m g:developers:rwx /shared/code/
# Xem ACL
getfacl /shared/code/
# Xóa ACL
setfacl -x u:alice /etc/app.conf
setfacl -b /etc/app.conf  # xóa toàn bộ ACL
```

---

## Kết hợp lệnh nâng cao (Pipes & Patterns)

```bash
# Tìm tất cả file setuid trong hệ thống (kiểm tra security)
find / -type f -perm /4000 2>/dev/null

# Tìm file không có owner (có thể là orphaned)
find / -nouser -o -nogroup 2>/dev/null

# Audit: tìm file world-writable
find / -type f -perm -o+w 2>/dev/null | grep -v /proc | grep -v /sys

# Fix quyền web server nhanh
find /var/www/html -type f | xargs chmod 644
find /var/www/html -type d | xargs chmod 755
chown -R www-data:www-data /var/www/html

# List user và UID của họ
awk -F: '$3 >= 1000 {print $1, $3}' /etc/passwd

# Tìm user chưa đặt password
sudo awk -F: '($2 == "" || $2 == "!") {print $1}' /etc/shadow

# Xem ai đang trong group sudo
getent group sudo

# Backup và restore ACL
getfacl -R /shared/ > acl_backup.txt
setfacl --restore=acl_backup.txt
```

---

## Lỗi thường gặp (Common Pitfalls)

**1. `usermod -G` thay vì `usermod -aG` xóa toàn bộ group cũ**
```bash
usermod -G docker alice    # NGUY HIỂM: alice chỉ còn trong group docker!
usermod -aG docker alice   # ĐÚNG: thêm docker vào groups hiện tại
```

**2. Thay đổi group không có hiệu lực ngay**
```bash
usermod -aG docker alice
# alice phải logout và login lại, hoặc:
su - alice       # mở session mới
newgrp docker    # activate group mới trong session hiện tại
```

**3. `chmod -R 777` trên thư mục web**
```bash
# Rất nguy hiểm! Ai cũng có thể upload malicious file
# Dùng:
find /var/www -type d -exec chmod 755 {} \;
find /var/www -type f -exec chmod 644 {} \;
```

**4. Quên `-a` khi `usermod -aG`**
```bash
# Xem groups trước khi thay đổi
id alice
# alice là: alice, developers, git
usermod -G docker alice   # bây giờ: alice, docker — mất developers và git!
```

**5. Permission của file private SSH key**
```bash
# SSH sẽ từ chối nếu private key quá permissive
chmod 644 ~/.ssh/id_rsa    # LỖI: "Permissions too open"
chmod 600 ~/.ssh/id_rsa    # ĐÚNG: chỉ owner đọc/ghi
chmod 700 ~/.ssh/          # thư mục .ssh
chmod 644 ~/.ssh/authorized_keys
```

**6. Nhầm `su` và `su -`**
```bash
su root      # switch user nhưng giữ environment variables của user cũ
su - root    # login shell: load /root/.profile, đổi $PATH, $HOME
# Thường cần `su -` để có đúng environment của root
```

**7. `setuid` trên script shell không hoạt động**
```bash
# Trên Linux, setuid bit bị ignore cho shell scripts
# Kernel chỉ honor setuid cho ELF binaries
# Dùng sudo config trong /etc/sudoers thay thế
```

---

## Câu hỏi phỏng vấn hay gặp

**Q1: Tại sao `ls -la /usr/bin/passwd` hiện `-rwsr-xr-x` mà không phải `-rwxr-xr-x`?**
> `passwd` cần ghi vào `/etc/shadow` (chỉ root có quyền). Bit `s` trong owner execute position là setuid. Khi user chạy `passwd`, process chạy với effective UID = root (owner), không phải UID của user gọi. Đây là cách Linux cho phép user đổi password của chính mình mà không cần quyền root trực tiếp.

**Q2: Sự khác biệt giữa `sudo su -` và `sudo -i`?**
> Cả hai đều cho root shell. `sudo su -` gọi `su -` dưới sudo, cần `/etc/sudoers` cho phép `su`. `sudo -i` trực tiếp tạo root login shell qua sudo, load `/root/.profile`. `sudo -i` clean hơn và preferred.

**Q3: Giải thích sticky bit trên thư mục `/tmp`?**
> `/tmp` là `drwxrwxrwt` — tất cả đều có write permission (tạo, sửa file). Sticky bit (`t`) đảm bảo chỉ owner của file hoặc root mới xóa/rename file trong `/tmp`. Alice không thể xóa file của Bob dù cả hai có write permission vào `/tmp`.

**Q4: `umask 027` nghĩa là gì? File mới tạo sẽ có permission gì?**
> `umask 027` = bỏ: owner nothing (0), group write (2), others rwx (7). File mới: 666 - 027 = 640 (rw-r-----). Dir mới: 777 - 027 = 750 (rwxr-x---). Group có thể đọc nhưng không ghi; others không có quyền gì.

**Q5: Khi nào dùng ACL thay vì standard permissions?**
> Standard permissions chỉ cho phép một owner, một group. ACL cần khi: cần cấp quyền cho nhiều user/group khác nhau trên cùng file; cần fine-grained permissions (user A: read-only, user B: read-write, group C: execute-only) mà không thể express bằng owner/group/others. Điều kiện: filesystem phải mount với option `acl`.

**Q6: `/etc/passwd` vs `/etc/shadow` — tại sao cần hai file?**
> Lịch sử: `/etc/passwd` lưu password hash, ai cũng đọc được (programs cần đọc username→UID mapping). Khi machine mạnh hơn, cracking hash trở nên dễ. Solution: tách hash sang `/etc/shadow` chỉ root đọc được. `/etc/passwd` giữ public info (username, UID, GID, home, shell), `/etc/shadow` giữ hash + password policy.
