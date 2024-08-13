# OpenWrt 下系统设置和常用软件



### acme
#### 步骤
1. 导入 CF_Token

export CF_Token="Y_jpG9AnfQmuX5Ss9M_qaNab6SQwme3HWXNDzRWs"

2. 切换为 letsencrypy  
acme.sh脚本默认ca服务器是zerossl，经常出错，会导致获取证书的时候一直出现：Pending, The CA is processing your order, please just wait.  

    ``` SHELL
    ~/acme.sh --set-default-ca --server letsencrypt
    ```

2. 生成证书
~/acme.sh --issue --dns dns_cf -d example.com -d '*.example.com'

acme.sh --install-cert -d example.com \
--key-file       /path/to/keyfile/in/nginx/key.pem  \
--fullchain-file /path/to/fullchain/nginx/cert.pem \
--reloadcmd     "docker restart nginx"

### nginx

#### http 自动跳转 https

``` conf
#  HTTP redirect
error_page 497 301 https://$host:$server_port$request_uri;
```

### vim

opkg update && opkg install vim-full diffutils

openwrt 在 /etc/shinit 中定义了vim为vi的别名, 删除那一行




