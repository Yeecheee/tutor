# OpenWrt 下系统设置和常用软件



### acme
#### 步骤

1. 安装 acme 脚本

    ``` SHELL
    apt update && apt -y install socat // 更新源并安装 socat

    wget -qO- get.acme.sh | bash // 安装 acme 脚本

    # 由于最新 acme.sh 脚本默认 ca 变成了 zerossl，现执行下面命令修改脚本默认 ca 为 letsencrypt
    ~/.acme.sh/acme.sh --set-default-ca --server letsencrypt
    ```

2. 导入 CF_Token  

    ``` SHELL
    export CF_Token="Y_jpG9AnfQmuX5Ss9M_qaNab6SQwme3HWXNDzRWs"
    ```

3. 生成证书  

    ``` SHELL
    ~/.acme.sh/acme.sh --issue --dns dns_cf -d example.com -d '*.example.com'
    ```

4. 安装证书到指定位置
    ``` SHELL
    ~/.acme.sh/acme.sh --install-cert -d example.com \
    --key-file       /path/to/keyfile/in/nginx/key.key  \
    --fullchain-file /path/to/fullchain/nginx/cert.crt \
    --reloadcmd     "docker restart nginx"
    # --reloadcmd "nginx -s reload"
    # --reloadcmd "systemctl restart nginx"
    ```

### nginx



#### 反向代理

``` nginx
server {
    listen 8888 ssl;
    server_name a.example.com;

    ssl_certificate /etc/nginx/ssl/1.crt;
    ssl_certificate_key /etc/nginx/ssl/1.key;

    location / {
        proxy_pass http://10.0.0.99:7777;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### http 自动跳转 https

``` nginx
http {
    #  HTTP redirect
    error_page 497 301 https://$host:$server_port$request_uri;
}

```

### Headscale & Headscale-ui

#### 参考链接
https://isedu.top/index.php/archives/192/#menu_index_5

#### 构建容器

``` YML
services:
  headscale:
    container_name: headscale
    # image 没有 latest tag
    # 到 https://hub.docker.com/r/headscale/headscale/tags 找 tag
    image: headscale/headscale:0.23.0-beta1-debug
    restart: unless-stopped
    # 老版本似乎是下面这个命令
    # command: headscale serve
    command: serve
    ports:
      - "AAAA:8080"

    volumes:
      - ./hs/config:/etc/headscale/
      - ./hs/data:/var/lib/headscale/

  headscale-ui:
    container_name: headscale-ui
    image: ghcr.io/gurucomputing/headscale-ui:latest
    restart: unless-stopped
    ports:
      - "BBBB:80"

  derper:
    image: fredliang/derper
    restart: unless-stopped
    ports:
      - 3478:3478/udp
      - CCCC:443 # 443 是默认 derpport
    environment:
      - DERP_DOMAIN=derp.example.com
```

#### headscale 配置文件

https://github.com/juanfont/headscale/raw/main/config-example.yaml

需要修改

``` YAML
server_url: https://hs.yourdomain.com   # FQDN, 指定访问的 domain  
listen_addr: 0.0.0.0:aaaa               # 监听 8080 端口  
ip_prefixes:                            # 调整 IP 显示，先显示 IPv4，而后IPv6  
  - 100.64.0.0/10
  - fd7a:115c:a1e0::/48
disable_check_updates: true             # 不检查 headscale 的新版本  
dns_config:
  override_local_dns: false             # 不要覆盖本地 DNS 服务
randomize_client_port: true             # 使用随机端口
```

derp.yml

``` YML
regions:
  901:
    regionid: 901
    regioncode: aaa
    regionname: bbb
    nodes:
      - name: 901a
        regionid: 901
        # 反代域名
        hostname: derp.example.com
        stunport: 3478
        stunonly: false
        # 反代端口
        derpport: RP_PORT
```

反代配置

``` nginx
server {
    listen RP_PORT ssl;

    server_name hs.example.com;

    ssl_certificate /etc/nginx/ssl/1.crt;
    ssl_certificate_key /etc/nginx/ssl/1.key;
    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://LAN_IP:AAAA;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header Host $host;
        proxy_redirect http:// https://;
        proxy_buffering off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        add_header Strict-Transport-Security "max-age=15552000; includeSubDomains" always;
    }

    location /web {
        proxy_pass http://LAN_IP:BBBB;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}


server {
    listen RP_PORT ssl;
    server_name derper.example.com;

    ssl_certificate /etc/nginx/ssl/1.crt;
    ssl_certificate_key /etc/nginx/ssl/1.key;

    location / {
        proxy_pass http://LAN_IP:CCCC;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```



### vim

``` SHELL
opkg update && opkg install vim-full diffutils
```

openwrt 在 `/etc/shinit` 中定义了 `vim` 为 `vi` 的别名, 删除那一行




