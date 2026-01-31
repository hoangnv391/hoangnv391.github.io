---
layout: post
title: Tạo SSH key và thêm SSH key vào tài khoản GitHub
categories: git
---
*( Cách tạo SSH key và thêm SSH vào tài khoản GitHub. Đảm bảo bạn đã cài đặt Git và có thể mở cửa sổ Git Bash ở trên máy tính )*

# Bước 1: Tạo SSH Key

Trong cửa số Git Bash

```bash
$ ssh-keygen -t rsa
```

```console
$ ssh-keygen -t rsa

Enter file in which to save the key (/root/.ssh/id_rsa): [Press enter]
Bash
Enter passphrase (empty for no passphrase): [Type a passphrase or not]
# Enter same passphrase again: [Type passphrase again or not]
Your identification has been saved in /root/.ssh/id_rsa.
# Your public key has been saved in /root/.ssh/id_rsa.pub.
```

# Bước 2: Thêm SSH key vào ssh-agent

Hãy mở Git Bash, trong cửa sổ Git Bash, đảm bảo rằng ssh-agent đã được kích hoạt bằng lệnh:

```bash
$ eval "$(ssh-agent -s)"
```

```console
$ eval "$(ssh-agent -s)"
Agent pid 1023 [May be diferent on your PC]
```

Thêm SSH key của bạn vào ssh-agent:

```bash
$ ssh-add.exe /c/Users/Admin/.ssh/id_rsa
```

```console
$ ssh-add.exe /c/Users/Admin/.ssh/id_rsa
Identity added: /c/Users/Admin/.ssh/id_rsa ([Your name])
```

# Bước 3: Thêm SSH public key vào tài khoản trên server của bạn (GitLab, GitHub, ...)

Nếu bạn đang sử dụng GitHub, truy cập vào địa chỉ [https://github.com/settings/keys](https://github.com/settings/keys) và chọn "New SSH key". phần Title chỉ là đặt tên thôi nên bạn muốn để tên gì cũng được, phần key nhập nội dung file id_rsa.pub.
