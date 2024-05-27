---
title: 搭建 apt 仓库
date: 2024-04-04 16:27:50
tags:
---

## 目标

手里有几个包，想搭建一个 apt 仓库，对应发行版为 Debian unstable riscv64

## 思路

这里的 server 指的是提供 apt 仓库服务的机器，client 指的是要访问 apt 仓库的机器

1. (server) 为本机生成 GPG 公钥与私钥
2. (server) 使用 aptly 添加包，并生成仓库的 snapshot
3. (server) 使用 nginx 搭建私有的服务器，存放生成的 snapshot
4. (client) 添加公钥，修改 apt 配置

## 步骤

### 1. 生成 gpg-key

#### 生成签名用的 GPG KEY

运行命令：`gpg --full-gen-key`，按照提示操作。

`~/.gnupg/openpgp-revocs.d/`目录下生成`.rev`的 key 文件

#### 生成 ASCII 格式的公钥

`gpg --output <key_filename> --export <your_email>`

请确保`<your_email>`与生成私钥时所用的邮箱相同

### 2. 使用 aptly 添加包，并生成仓库的 snapshot

安装 aptly 后，创建仓库，本步骤执行后将生成`~/.aptly`目录

```bash
# 请拓展尖括号的内容, 例如，我这边拓展后是这样的
# aptly repo create -architectures riscv64 -comment 'private riscv64 repo' -component main -distribution unstable my_repo
aptly repo create -architectures <arch> -comment <your_comment> -component <component> -distribution <distrubution> <repo_name>
```

向仓库内添加若干包，本步骤执行后将在`~/.aptly/pool`目录下存放所添加包的拷贝

```bash
aptly repo add <repo_name> <pkg_path>
```

创建仓库 snapshot 并发布，此过程会要求使用 GPG 私钥签名，所以会提示输入生成 GPG 私钥时的密码

发布后的仓库在`~/.aptly/pool`

```bash
aptly snapshot create <snapshot_name> from repo <repo_name>
aptly publish -architectures <arch> snapshot <snapshot_name>
```

### 3. 搭建服务器

使用 nginx 搭建文件服务器，使得外部网络可以访问仓库内容

```bash
sudo apt-get install nginx -y
# 修改 root /var/www/html; 为目标目录
# 并在添加下面一行
# autoindex on;
sudo vim /etc/nginx/sites-enabled/default

sudo systemctl restart nginx.service

# 别忘了用浏览器访问一下
# http://<ip_address>/public
```

### 4. 在 client 端访问 apt 仓库

注意，服务器我们已经搭建好了，接下来的操作在客户端上进行

将前文中的公钥添加至本机配置，**TODO：使用 DEB-822 格式，添加**

```bash
sudo apt-key add <key_filename>
```

在 apt 配置中写入如下一行

```bash
# 例如，我这边的是 deb http://10.9.127.200/public/ unstable main
deb <server_path> <distrubution> <component>
```

## 已知问题

**TODO**

1. 需要对 aptly 进行进一步的解释
2. 需要对 gpg 签名的原理进行进一步的解释

## 参考资料

- [aptly 搭建本地仓库 - vimer](https://www.aftermath.cn/2023/06/03/aptly-usage/)
- [动手搭建一个私有的 apt 服务器 - csdn](https://blog.csdn.net/weixin_44979075/article/details/124747355)
- [手动自建 APT 仓库步骤 - 趣小组](https://talk.quwj.com/topic/1897)
- [生成新 GPG 密钥 - GitHub](https://docs.github.com/zh/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)
