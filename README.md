# Naiveproxy-behind-Nginx
示例用nginx反代naiveproxy(Caddy2)
由于Nginx是比Caddy更常用的反代服务，所以想让网站和其他服务与naiveproxy(Caddy2)共存，并且仍然用Nginx前置分流。在此例中Caddy2仅提供naiveproxy的功能。


# 编译同时带有forwardproxy和proxyprotocol的caddy2
apt install golang-go 

go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest 

cd ~/go

~/go/bin/xcaddy build --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive --with github.com/mastercactapus/caddy2-proxyprotocol

chmod +x caddy

mv caddy /usr/bin/

mkdir /etc/caddy

# caddy配置守护进程(开机自启)

groupadd --system caddy
useradd --system \
    --gid caddy \
    --create-home \
    --home-dir /var/lib/caddy \
    --shell /usr/sbin/nologin \
    --comment "Caddy web server" \
    caddy

# 创建 caddy.service 文件，路径：/etc/systemd/system/。填入以下内容:
[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target

[Service]
User=caddy
Group=caddy
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/caddy.json
ExecReload=/usr/bin/caddy reload --config /etc/caddy/caddy.json
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target

# 设置开机自启动
systemctl daemon-reload
systemctl enable caddy









