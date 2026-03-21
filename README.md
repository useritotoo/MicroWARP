# MicroWARP 🚀

[![Docker Pulls](https://img.shields.io/badge/docker-ready-blue.svg)](https://github.com/ccbkkb/MicroWARP/packages)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[English](#english) | [中文说明](#chinese)

<a name="english"></a>
## 🇬🇧 English

An ultra-lightweight, high-performance Cloudflare WARP SOCKS5 proxy in Docker. 
A perfect drop-in replacement for `caomingjun/warp`.

### 🌟 Why MicroWARP?

Many popular WARP Docker images (like `caomingjun/warp`) rely on the official Cloudflare `warp-cli` daemon. This results in heavy memory usage (often **150MB+**) and potential process deadlocks under high concurrency.

**MicroWARP** does things differently:
1. **Kernel-Level WireGuard**: It drops the bloated official client and uses Linux's native `wg0` interface. CPU usage is almost zero.
2. **MicroSOCKS**: It uses a pure C-based `microsocks` server instead of heavy Go/Rust proxies.
3. **Extreme Low RAM**: Runs smoothly on **< 5MB RAM**. Perfect for 1C1G cheap VPS.
4. **Multi-Arch**: Native support for `amd64` and `arm64` (Oracle Cloud ARM ready).

### 📦 Quick Start

You can seamlessly replace your existing WARP proxy. Just map port `1080` and give it `NET_ADMIN` privileges. Create a `docker-compose.yml`:

```yaml
vservices:
  microwarp:
    image: ghcr.io/ccbkkb/MicroWARP:latest
    container_name: microwarp
    restart: always
    ports:
      - "1080:1080" # Standard SOCKS5 port (No Auth)
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    volumes:
      - warp-data:/etc/wireguard # Keep account data

volumes:
  warp-data:
```

Run the container:
```bash
docker compose up -d
```

Once running, configure your apps (Telegram, v2ray, Xray, AIzaSy) to use `socks5://127.0.0.1:1080`. Your traffic is now securely routed through Cloudflare's backbone!

### 📝 Auto-Registration
Zero configuration required. On the first run, MicroWARP will automatically register a free WARP account and persist the configuration in the Docker volume.

---

<a name="chinese"></a>
## 🇨🇳 中文说明

一个超轻量、高性能的 Cloudflare WARP SOCKS5 Docker 代理。
完美平替 `caomingjun/warp` 的终极方案。

### 🌟 为什么选择 MicroWARP？

市面上流行的 WARP 镜像（例如 `caomingjun/warp`）绝大多数打包了 Cloudflare 官方的 `warp-cli` 守护进程。这会导致极高的内存占用（通常在 **150MB 以上**），并且在高并发下极易发生进程死锁和崩溃。

**MicroWARP** 采用了完全不同的极客底层架构：
1. **内核级 WireGuard**：彻底抛弃臃肿的官方客户端，直接调用 Linux 原生内核态的 `wg0` 网卡接管流量，CPU 损耗近乎为零。
2. **MicroSOCKS 引擎**：使用纯 C 语言编写的 `microsocks` 服务器替代繁重的 Go/Rust 代理引擎。
3. **极致极低内存**：高并发下内存占用依然 **< 5MB**。专为 1C1G 的廉价小内存 VPS 拯救者。
4. **多架构支持**：原生支持 `amd64` 和 `arm64`（完美兼容甲骨文免费 ARM 机器）。

### 📦 快速开始

你可以零成本无缝替换掉现有的 WARP 代理。只需映射 `1080` 端口并赋予容器 `NET_ADMIN` 网络管理权限。新建一个 `docker-compose.yml`：

```yaml
services:
  microwarp:
    image: ghcr.io/ccbkkb/MicroWARP:latest
    container_name: microwarp
    restart: always
    ports:
      - "1080:1080" # 标准的无密码 SOCKS5 端口
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    volumes:
      - warp-data:/etc/wireguard # 持久化保存账号凭证

volumes:
  warp-data:
```

启动容器：
```bash
docker compose up -d
```

启动后，将你的应用（Telegram、v2ray、Xray、AIzaSy 等）的 SOCKS5 代理指向 `127.0.0.1:1080`，你的出站流量就已经被 Cloudflare 骨干网完美接管并洗白了！

### 📝 全自动免配置
你不需要手动提取任何密钥。首次启动时，MicroWARP 会在后台全自动向 Cloudflare 申请注册免费 WARP 账户，提取节点信息，并永久保存在本地的数据卷中。
