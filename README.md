# install shadowsocks
```
> yum install epel-release
> yum install -y python-pip
> pip install shadowsocks
```
```
> vi /etc/shadowsocks.json
{
    "server": "0.0.0.0",
    "server_port": {port}, 
    "local_address": "127.0.0.1", 
    "local_port": 1099,
    "password": "password",
    "timeout": 300,
    "method": "aes-256-cfb"
}
```
```
> cd /etc/systemd/system/
> touch shadowsocks.service 
```
```
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

[Install]
WantedBy=multi-user.target
```
```
> chmod +x /etc/systemd/system/shadowsocks.service
> systemctl enable shadowsocks
```



# install shadowsocks-libev $ v2ray-plugin

> OS: CentOS 7+

## env：
```
> yum install epel-release -y
> yum install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel libsodium-devel mbedtls-devel -y
````
## download source
``` 
> yum install git
> cd /usr/local/src
> git clone https://github.com/shadowsocks/shadowsocks-libev.git
```
## compile
```
> cd /usr/local/src/shadowsocks-libev
> git submodule update --init --recursive
> sh autogen.sh
> ./configure --disable-documentation
> make
> make install
```
## copy config file
```
> cp /usr/local/src/shadowsocks-libev/debian/config.json /etc/shadowsocks-libev/config.json
```

## v2ray-plugin
download v2ray-plugin from github release,choose linux-amd64 version
url:  https://github.com/shadowsocks/v2ray-plugin/releases  
```
> mv v2ray-plugin_linux_amd64 v2ray-plugin
> cp v2ray-plugin /usr/bin/v2ray-plugin
```

## setting
```
{
    "server": "0.0.0.0",
    "nameserver": "8.8.8.8", 
    "server_port":443,
    "local_port":1080,
    "password":"{password}",
    "timeout":86400,
    "method":"chacha20-ietf-poly1305",
    "plugin":"v2ray-plugin",
    "plugin_opts":"server;tls;fast-open;host={domain.tld};cert=/etc/shadowsocks-libev/fullchain.cer;key=/etc/shadowsocks-libev/{domain.tld}.key;loglevel=none"
}
```

## apply TLS certification
> login cloudflare,click your account -> My Profile->API KEYs->Global API KEY
copy your API KEY,and remember your CloundFlare email address.
```
> export CF_Email="your CloundFlare email address"
> export CF_Key="your CloundFlare API KEY"
> curl https://get.acme.sh | sh
> ~/.acme.sh/acme.sh --issue --dns dns_cf -d {domain.tld}
```

## copy TLS certification
```
cp /root/.acme.sh/{domain.tld}/fullchain.cer /etc/shadowsocks-libev/
cp /root/.acme.sh/{domain.tld}/{domain.tld}.key /etc/shadowsocks-libev/
```

## register service
```
> vi /etc/systemd/system/shadowsocks.service
```
```
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/local/bin/ss-server -c /etc/shadowsocks-libev/config.json 

[Install]
WantedBy=multi-user.target
```
```
> chmod +x /etc/systemd/system/shadowsocks.service
> systemctl enable shadowsocks
```
## firewall setting
```
> firewall-cmd --zone=public --add-port=443/tcp --permanent
> firewall-cmd --reload
> systemctl shadowsocks restart
```

# shadowsocksx-ng(1.8.2)
## copy v2ray-plugin
download v2ray-plugin from github release,choose darwin_amd64 version.
url:  https://github.com/shadowsocks/v2ray-plugin/releases 

```
> mv v2ray-plugin_darwin_amd64 v2ray-plugin
> cp v2ray-plugin ~/Library/Application Support/ShadowsocksX-NG/plugins
```
local config
```
{
  "method" : "chacha20-ietf-poly1305",
  "local_address" : "127.0.0.1",
  "local_port" : 1086,
  "server_port" : 443,
  "server" : "{domain.tld}",
  "password" : "{password}",
  "plugin" : "plugins/v2ray-plugin",
  "plugin_opts" : "tls;host={domain.tld}",
  "timeout" : 60
}
```
