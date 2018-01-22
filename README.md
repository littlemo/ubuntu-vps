# Ubuntu-VPS

容器化的虚拟专用服务器，基于 [Ubuntu 官方镜像](https://registry.hub.docker.com/_/ubuntu/) 构建。

## 镜像标签(tags)

- `littlemo/ubuntu-vps:latest` (xenial)
- `littlemo/ubuntu-vps:16.04`  (xenial)

## 已安装的软件包声明
### 基础包

- [Xenial (16.04) minimal](http://packages.ubuntu.com/xenial/ubuntu-minimal)

### 额外包

- [openssh-server](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring)
- [landscape-common](https://packages.ubuntu.com/xenial/landscape-common)
- [sudo](https://packages.ubuntu.com/xenial/sudo)

## 额外说明

### 系统监控

为更好的掌握系统信息，安装了 `landscape-common` 包，可在登录时显示该容器的系统资源状态

### SSH相关

为保证在公网环境下部署的安全，该容器 __禁止__ 用户的密码登录，仅可使用秘钥进行SSH远程登录，并暴露 `22` 端口用于接受连接。

### 用户&组

本镜像除 `root` 账户外，额外创建了用户 `dev` ，并将 `dev` 添加到了 `adm` & `sudo` 用户组中。从而便于开发使用，建议默认使用 `dev` 账户进行 SSH 远程登录，并在启动容器时挂载相应的公钥到 `/home/dev/.ssh/authorized_keys` (建议只读挂载)。

为应急需求，设置了 `root` 账户的默认密码。

同样为便于开发， 设置 `sudo` 用户组可以免密执行 `sudo` 命令

默认密码：
- root: __whosyourdaddy__
- dev: __dev__

## 启动容器

### docker-cli

```bash
$ docker run -d -p 10022:22 -v /paht/to/ssh/publickey:/home/dev/.ssh/authorized_keys --name vps littlemo/ubuntu-vps:latest

$ ssh vps_name_in_your_ssh_config
dev@1a1aa896bb17:~$
```

### docker-compose

```yml
version: '2'
services:
  vps:
    image: littlemo/ubuntu-vps:latest
    container_name: vps
    restart: unless-stopped  # 重启策略
    ports:
      - "10022:22"
      - "10000:10000"  # 与10001端口一起，用于满足之后可能存在的端口映射需求
      - "10001:10001"
    volumes:
      # 挂载持久化文档路径
      - ./mnt/mnt:/mnt:rw

      # 挂载用于登录的公钥文件
      - ./mnt/ssh/authorized_keys:/home/dev/.ssh/authorized_keys:ro
```

## 问题

如果您在使用该镜像时遇到任何问题，请查看镜像源码的 [littlemo/ubuntu-vps](https://github.com/littlemo/ubuntu-vps) Repo，并在其中提交 Issues 给我，多谢您的帮助~~
