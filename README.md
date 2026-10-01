xray-vless-vision-bilingual
===========================

An Xray-core server configuration template (VLESS + XTLS Vision + TLS)
with every setting explained in both English and Chinese.

一个 Xray-core 服务端配置模板（VLESS + XTLS Vision + TLS），
每一项配置都附有中英文双语注释。


WHAT'S INSIDE / 文件说明
------------------------

config.json   Server config with bilingual comments on every block and key
              带中英文逐项注释的服务端配置
README.txt    This file / 本说明文件

Xray accepts // comments in its JSON config, so the file can be used as-is.
GitHub may highlight the comments as syntax errors; that is expected.

Xray 支持在 JSON 配置中使用 // 注释，文件可直接使用。
GitHub 可能会把注释标红为语法错误，属于正常现象。


FEATURES / 功能
---------------

- VLESS + XTLS Vision + TLS inbound on port 443, with fallbacks to a local
  web server so the port looks like a normal HTTPS site
  443 端口 VLESS + XTLS Vision + TLS 入站，非代理流量回落到本地网站，伪装成普通 HTTPS 站点

- Per-domain DNS: Gemini / DeepMind via Google DoH (IPv4 only),
  Netflix / Perplexity via Cloudflare DoH (IPv4 only), OpenAI via
  Cloudflare DoH (IPv6 only), China domains via AliDNS
  按域名分流 DNS：Gemini / DeepMind 走 Google DoH（仅 IPv4），
  Netflix / Perplexity 走 Cloudflare DoH（仅 IPv4），OpenAI 走 Cloudflare DoH（仅 IPv6），
  国内域名走阿里 DNS

- Blocks BitTorrent, private / LAN addresses, China IPs, Steam downloads
  and Adobe activation servers
  屏蔽 BT、私有 / 局域网地址、中国 IP、Steam 下载和 Adobe 激活服务器

- Traffic stats per user, inbound and outbound through a local-only API
  (127.0.0.1:10085)
  通过仅本机可访问的 API（127.0.0.1:10085）按用户、入站、出站统计流量

- Optional sections, commented out: Cloudflare WARP (WireGuard) outbound,
  VLESS chain proxy, public DNS relay on port 53, DNS-over-TCP forwarding
  可选功能（已注释）：Cloudflare WARP（WireGuard）出站、VLESS 链式代理、
  53 端口 DNS 中转、通过 TCP 转发 DNS 查询


REQUIREMENTS / 环境要求
-----------------------

- A recent Xray-core release. This config uses the "tunnel" protocol name;
  older versions call it "dokodemo-door" (an example is kept at the end of
  config.json).
  较新版本的 Xray-core。本配置使用 "tunnel" 协议名；旧版本叫 "dokodemo-door"
  （config.json 末尾保留了旧写法示例）。

- A domain pointing to the server, and a TLS certificate for it
  (e.g. from Let's Encrypt)
  一个解析到服务器的域名，以及该域名的 TLS 证书（如 Let's Encrypt）

- A web server (e.g. Nginx) listening on 127.0.0.1:23332 (HTTP/1.1) and
  127.0.0.1:23333 (HTTP/2), with PROXY protocol enabled, to receive fallbacks
  一个网站服务（如 Nginx），监听 127.0.0.1:23332（HTTP/1.1）和 127.0.0.1:23333（HTTP/2），
  并开启 PROXY protocol，用于接收回落流量


QUICK START / 快速开始
----------------------

1. Install Xray-core
   安装 Xray-core

   bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install

2. Generate a UUID for each user
   为每个用户生成 UUID

   xray uuid

3. Edit config.json and replace the placeholders
   编辑 config.json，替换以下占位符

   your_uuid1 / your_uuid2 / your_uuid3   -> UUIDs from step 2 / 第 2 步生成的 UUID
   your.domain.com                        -> your domain / 你的域名
   /usr/local/etc/xray/fullchain.pem      -> your certificate path / 你的证书路径
   /usr/local/etc/xray/privkey.pem        -> your private key path / 你的私钥路径

   Only if you enable the optional sections / 仅在启用可选功能时需要：
   YOUR_WARP_PRIVATE_KEY, YOUR_WARP_IPV6  -> your WARP account values / 你的 WARP 账户信息
   203.0.113.10                           -> your DNS server IP / 你的 DNS 服务器 IP
   chain.example.com, your_uuid           -> your chain server / 你的链式代理服务器

4. Copy the config and test it
   复制配置并检查语法

   cp config.json /usr/local/etc/xray/config.json
   xray run -test -c /usr/local/etc/xray/config.json

5. Restart Xray
   重启 Xray

   systemctl restart xray
   systemctl status xray


CHECK TRAFFIC STATS / 查看流量统计
----------------------------------

   xray api statsquery --server=127.0.0.1:10085


SECURITY NOTES / 安全提示
-------------------------

- Never commit real UUIDs, private keys or certificates to a public repo.
  不要把真实的 UUID、私钥或证书提交到公开仓库。

- Keep the API inbound on 127.0.0.1. Exposing it lets anyone add or remove
  users on your server.
  API 入站保持监听 127.0.0.1。对外开放会让任何人都能增删你服务器上的用户。

- If you enable the public DNS relay on port 53, keep it TCP only.
  Open UDP resolvers are abused for DNS amplification attacks.
  如果启用 53 端口 DNS 中转，只开 TCP。开放的 UDP 解析器会被用于 DNS 放大攻击。


REFERENCES / 参考
-----------------

Xray-core:           https://github.com/XTLS/Xray-core
Xray documentation:  https://xtls.github.io
