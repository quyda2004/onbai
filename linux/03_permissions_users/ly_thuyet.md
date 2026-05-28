# Linux Permissions & Users — Quyền hạn và Người dùng

---

## Giải thích cho người mới

Linux giống như một tòa nhà chung cư có bảo vệ. Mỗi file/thư mục có "chủ nhân" (owner) và có quy định ai được đọc, ai được sửa, ai được chạy.

**3 loại người:**
- **Owner** (u): chủ sở hữu file
- **Group** (g): nhóm mà chủ thuộc về
- **Others** (o): tất cả người còn lại

**3 loại quyền:**
- **r** (read = 4): đọc
- **w** (write = 2): ghi, sửa, xóa
- **x** (execute = 1): chạy (execute file hoặc vào thư mục)

---

## Đọc hiểu permissions

```
-rwxr-xr--  1  alice  developers  4096  Jan 15  script.sh
│└─┬──┘└─┬──┘└─┬──┘
│  │      │     └── others: r-- (chỉ đọc)
│  │      └──── group: r-x (đọc + execute)
│  └────────── owner: rwx (đọc + ghi + execute)
└── type: - (file), d (dir), l (symlink)
```

**Numeric notation:**
```
rwx = 4+2+1 = 7
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
--- = 0+0+0 = 0

-rwxr-xr-- = 754
drwxr-xr-x = 755 (typical directory)
-rw-r--r-- = 644 (typical file)
-rw------- = 600 (private file)
-rwx------ = 700 (private executable)
```

---

## Giải thích nâng cao

**Special permissions:**
- **SUID (4xxx)**: khi user chạy file, process chạy với quyền của **owner** (thường root). Ví dụ: `/usr/bin/passwd` (SUID root để đổi password). Hiển thị: `rws` thay vì `rwx`.
- **SGID (2xxx)**: process chạy với quyền group của file. Với directory: file mới tạo trong đó inherit group của directory.
- **Sticky bit (1xxx)**: với directory: chỉ owner của file mới xóa được file của mình (dù others có write). Dùng cho `/tmp`. Hiển thị: `rwt` hoặc `rwT`.

**umask**: giá trị "trừ" đi khi tạo file/dir mới.
```
umask 022:
  File mới: 666 - 022 = 644 (rw-r--r--)
  Dir mới:  777 - 022 = 755 (rwxr-xr-x)
```

**ACL (Access Control List)**: kiểm soát quyền chi tiết hơn chuẩn unix — cho phép set quyền cho **nhiều user/group cụ thể** (không chỉ owner/group/others).

---

## BẢNG LỆNH THỰC HÀNH

### chmod — Change Mode (thay đổi quyền)
```bash
# Symbolic mode
chmod u+x script.sh            # thêm execute cho owner
chmod g+w file.txt             # thêm write cho group
chmod o-r file.txt             # bỏ read của others
chmod a+r file.txt             # thêm read cho all (a = ugo)
chmod u=rwx,g=rx,o=r file.txt  # set quyền cụ thể cho từng loại
chmod +x script.sh             # thêm execute cho tất cả (= a+x)
chmod -x script.sh             # bỏ execute cho tất cả
chmod u+x,o-w file.txt         # kết hợp

# Numeric mode
chmod 755 script.sh            # rwxr-xr-x
chmod 644 file.txt             # rw-r--r--
chmod 600 ~/.ssh/id_rsa        # rw------- (private key PHẢI là 600)
chmod 700 ~/.ssh               # rwx------ (SSH dir)
chmod 777 file.txt             # rwxrwxrwx (NGUY HIỂM — tránh dùng)
chmod 000 file.txt             # ---------- (không ai truy cập)

# Recursive
chmod -R 755 directory/        # áp dụng cho tất cả trong thư mục
chmod -R u+rw,go+r directory/  # recursive symbolic

# Special permissions
chmod 4755 /usr/bin/program    # SUID: rws r-x r-x
chmod 2755 /usr/bin/program    # SGID: rwx r-s r-x
chmod 1777 /tmp                # sticky: rwx rwx rwt

# Chỉ file (không directory) hoặc chỉ directory
find . -type f -exec chmod 644 {} \;
find . -type d -exec chmod 755 {} \;
```

### chown — Change Owner
```bash
chown alice file.txt           # đổi owner
chown alice:developers file.txt    # đổi owner và group
chown :developers file.txt     # chỉ đổi group
chown -R alice:alice directory/ # recursive
chown --from=olduser newuser file.txt  # chỉ đổi nếu đang là olduser

# Chỉ root mới chạy được chown
sudo chown root:root /etc/hosts
```

### chgrp — Change Group
```bash
chgrp developers file.txt      # đổi group
chgrp -R developers directory/ # recursive
```

### umask — Default permissions
```bash
umask                          # xem umask hiện tại (022)
umask 022                      # set umask (file: 644, dir: 755)
umask 027                      # file: 640, dir: 750
umask 077                      # file: 600, dir: 700 (strict)
umask -S                       # symbolic display (u=rwx,g=rx,o=rx)
```

### id / whoami / groups — Thông tin user hiện tại
```bash
whoami                         # in tên user hiện tại
id                             # uid=1000(alice) gid=1000(alice) groups=...
id alice                       # thông tin của user alice
groups                         # danh sách groups của user hiện tại
groups alice                   # groups của user alice
```

### User management
```bash
# Tạo user
sudo useradd alice                         # tạo user (không home dir, không password)
sudo useradd -m alice                      # tạo kèm home dir
sudo useradd -m -s /bin/bash alice         # chỉ định shell
sudo useradd -m -G sudo,developers alice   # thêm vào groups
sudo adduser alice                         # interactive (friendlier) — Debian/Ubuntu

# Đổi password
passwd                         # đổi password của mình
sudo passwd alice              # đổi password của alice (root)
sudo passwd -l alice           # lock account
sudo passwd -u alice           # unlock account
sudo passwd -e alice           # expire → user phải đổi khi login tiếp

# Xóa user
sudo userdel alice             # xóa user (giữ home dir)
sudo userdel -r alice          # xóa user + home dir + mail

# Sửa user
sudo usermod -aG sudo alice    # thêm alice vào group sudo (-a = append!)
sudo usermod -G docker,alice alice  # set groups (KHÔNG -a → xóa groups cũ)
sudo usermod -s /bin/zsh alice # đổi default shell
sudo usermod -l newname alice  # đổi username
sudo usermod -d /new/home -m alice  # đổi home dir và move

# QUAN TRỌNG: -aG vs -G
sudo usermod -aG docker alice  # ĐÚNG: append group docker
sudo usermod -G docker alice   # SAI: xóa hết groups cũ, chỉ còn docker!
```

### Group management
```bash
sudo groupadd developers       # tạo group
sudo groupdel developers       # xóa group
sudo groupmod -n newname developers  # đổi tên group

# Xem members của group
getent group developers        # developers:x:1001:alice,bob
grep "^developers:" /etc/group # cách khác
```

### /etc/passwd, /etc/shadow, /etc/group
```bash
# /etc/passwd — thông tin user (không có password hash)
# alice:x:1000:1000:Alice Smith:/home/alice:/bin/bash
# name:password:uid:gid:gecos:home:shell
cat /etc/passwd
getent passwd alice            # truy vấn user info (hỗ trợ LDAP, NIS)

# /etc/shadow — password hash (chỉ root đọc được)
sudo cat /etc/shadow

# /etc/group — group info
cat /etc/group
getent group developers
```

### sudo — Super User Do
```bash
sudo command                   # chạy command với quyền root
sudo -u alice command          # chạy với quyền của alice
sudo -i                        # interactive root shell (login shell)
sudo su                        # switch to root
sudo su - alice                # switch to alice (login shell)
sudo -l                        # liệt kê quyền sudo của user hiện tại
sudo -l -U alice               # quyền sudo của alice (cần root)
sudo !!                        # chạy lệnh trước với sudo

# Cấu hình sudo: /etc/sudoers (PHẢI dùng visudo để sửa!)
sudo visudo                    # mở /etc/sudoers an toàn
# alice ALL=(ALL:ALL) ALL      → alice có thể sudo mọi thứ
# alice ALL=(ALL) NOPASSWD: /usr/bin/apt → không cần password
```

### ACL — Access Control List
```bash
# Cần package acl
getfacl file.txt               # xem ACL
setfacl -m u:bob:rw file.txt   # cho bob read+write
setfacl -m g:devs:r file.txt   # cho group devs read
setfacl -x u:bob file.txt      # xóa ACL của bob
setfacl -b file.txt            # xóa tất cả ACL
setfacl -R -m u:bob:rX dir/   # recursive (X = execute chỉ nếu là dir)
setfacl -d -m u:bob:rw dir/   # default ACL cho file mới trong dir
```

### SSH Key Permissions — BẮT BUỘC
```bash
# SSH sẽ từ chối nếu permissions sai
chmod 700 ~/.ssh               # thư mục .ssh
chmod 600 ~/.ssh/id_rsa        # private key (chỉ owner đọc được)
chmod 644 ~/.ssh/id_rsa.pub    # public key
chmod 600 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/known_hosts
chmod 600 ~/.ssh/config
```

---

## Khi nào dùng gì

| Tình huống | Lệnh |
|-----------|------|
| Cho phép chạy script | `chmod +x script.sh` |
| File private (SSH key) | `chmod 600 file` |
| Web files | `chmod 644 file`, `chmod 755 dir` |
| Thêm user vào group | `sudo usermod -aG groupname username` |
| Check quyền SSH | `ls -la ~/.ssh/` |
| Tạo shared dir cho team | `chmod 2775 dir/` (SGID) |
| Temporary dir | `chmod 1777 /tmp` (sticky) |

---

## Lỗi thường gặp

```bash
# SSH: UNPROTECTED PRIVATE KEY FILE
# → Permission của private key phải là 600
chmod 600 ~/.ssh/id_rsa

# Permission denied khi chạy script
# → Thiếu execute permission
chmod +x script.sh

# usermod -G thay vì -aG → xóa hết groups cũ
sudo usermod -aG docker alice  # luôn dùng -a khi thêm group

# Chỉnh sửa /etc/sudoers trực tiếp
# → NGUY HIỂM: dùng visudo để có syntax check
sudo visudo

# sudo: command not found sau khi switch user
sudo -i                        # login shell có đầy đủ PATH
```

---

## Câu hỏi phỏng vấn hay gặp

- Giải thích `rwxr-xr--` dưới dạng số?
- SUID bit là gì? Tại sao `/usr/bin/passwd` cần SUID?
- Sticky bit trên `/tmp` có tác dụng gì?
- `usermod -aG` vs `usermod -G` — khác nhau thế nào?
- umask 022 nghĩa là gì cho file mới?
- Tại sao SSH private key phải là 600?
