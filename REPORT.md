# Session 09 - Bài 1: Kiểm tra cài đặt Docker Engine và chạy Container Hello-World

## 1. Môi trường

| Thành phần | Giá trị |
|---|---|
| Máy chủ | VPS Ubuntu 22.04.5 LTS (hostname `wean2607`) |
| User | `devops` (đã thêm vào group `docker`, chạy lệnh không cần `sudo`) |
| Docker | Docker Engine - Community 29.9.0 (containerd v2.4.1, runc 1.5.2) |
| Phần cứng | 1 CPU, 1.918 GiB RAM, kernel 5.15.0-198-generic |

## 2. Cài đặt và cấp quyền không cần sudo

```bash
curl -fsSL https://get.docker.com | sudo sh
sudo usermod -aG docker $USER
newgrp docker
docker ps
```

```text
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

`docker ps` chạy bằng user `devops` không cần `sudo` và không báo lỗi `permission denied`, chứng tỏ CLI đã kết nối được tới Docker Daemon qua `/var/run/docker.sock`.

## 3. `docker version`

```bash
docker version
```

```text
Client: Docker Engine - Community
 Version:           29.9.0
 API version:       1.56
 Go version:        go1.26.9
 Git commit:        f415da8
 Built:             Thu Oct  8 20:38:01 2026
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          29.9.0
  API version:      1.56 (minimum version 1.40)
  Go version:       go1.26.9
  Git commit:       a5b58c9
  Built:            Thu Oct  8 20:38:01 2026
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v2.4.1
  GitCommit:        f2551031d7276a770f65f98c9b52e57e7dad07e8
 runc:
  Version:          1.5.2
  GitCommit:        v1.5.2-0-g29dd3dc2
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

![docker version](images/01-docker-version.png)

Lệnh hiển thị cả hai phần **Client** (Docker CLI) và **Server** (Docker Engine). Có phần Server tức là CLI đã kết nối thành công tới Daemon.

## 4. `docker info`

```bash
docker info
```

```text
Client: Docker Engine - Community
 Version:    29.9.0
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.38.0
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.6.0
    Path:     /usr/libexec/docker/cli-plugins/docker-compose
  model: Docker Model Runner (Docker Inc.)
    Version:  v1.2.6
    Path:     /usr/libexec/docker/cli-plugins/docker-model

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 1
 Server Version: 29.9.0
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: f2551031d7276a770f65f98c9b52e57e7dad07e8
 runc version: v1.5.2-0-g29dd3dc2
 init version: de40ad0
 Security Options:
  apparmor
   Profile: default
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 5.15.0-198-generic
 Operating System: Ubuntu 22.04.5 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 1.918GiB
 Name: wean2607
 ID: f19a0ef4-d29c-461b-9f71-ed241b1db4ca
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
  ::1/128
 Live Restore Enabled: false
 Firewall Backend: iptables
  EnableUserlandProxy: true
  UserlandProxyPath: /usr/bin/docker-proxy
```

![docker info (phần 1)](images/02-docker-info.png)

![docker info (phần 2)](images/02b-docker-info.png)

Thông tin chính: Server Version 29.9.0, Storage Driver `overlayfs`, Cgroup Driver `systemd` (cgroup v2), Docker Root Dir `/var/lib/docker`, chạy trên Ubuntu 22.04.5 LTS.

## 5. `docker run hello-world`

```bash
docker run hello-world
```

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

![docker run hello-world](images/03-hello-world.png)

Các bước Docker thực hiện:

1. Docker Client gửi yêu cầu tới Docker Daemon.
2. Daemon không tìm thấy image `hello-world` ở máy nên tải về từ Docker Hub.
3. Daemon tạo container mới từ image và chạy chương trình bên trong.
4. Output của container được truyền về Client và in ra Terminal.

## 6. Lưu kết quả và kiểm tra

Toàn bộ output của 3 lệnh được ghi vào file `~/session09-ex1/REPORT.md` trên máy chủ, sau đó kiểm tra bằng:

```bash
grep -n "Hello from Docker" REPORT.md
```

```text
122:Hello from Docker!
```

![docker ps và kiểm tra REPORT](images/04-docker-ps-report.png)

## 7. Kết luận

- User `devops` chạy được các lệnh `docker` mà không cần `sudo`.
- `docker version` hiển thị đủ Client và Server.
- `docker run hello-world` in ra dòng **"Hello from Docker! This message shows that your installation appears to be working correctly."**, nghĩa là Docker Engine hoạt động đúng.
