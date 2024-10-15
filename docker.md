


### Teamspeak3



### Matrix

https://iwanlab.com/docker-compose-install-matrix-element/

### Rustdesk

``` yml
services:
  hbbs:
    container_name: hbbs
    network_mode: host

    image: rustdesk/rustdesk-server
    command: hbbs
    volumes:
      - ./hbbs:/root # 自定义挂载目录
    depends_on:
      - hbbr
    restart: unless-stopped

  hbbr:
    container_name: hbbr
    network_mode: host
    image: rustdesk/rustdesk-server
    command: hbbr
    volumes:
      - ./hbbr:/root # 自定义挂载目录
    restart: unless-stopped
```
然后在 `./hbbs` 文件夹下找到 `.pub` 文件,其中内容为连接时的 `key`
### subconverter
enable_cache = false

### Jellyfin

``` yml
services:
  Jellyfin:
    container_name: Jellyfin
    image: nyanmisaka/jellyfin
    restart: unless-stopped
    privileged: true
    ports:
      - "xxxxx:8096"
    volumes:
      - ./config:/config
      - ./cache:/cache
      - ./custom:/custom
      - /volume2/RSS:/RSS
      - /volume3/Media:/Media

    environment:
      - UID=1000
      - GID=1000
    devices:
      - /dev/dri:/dev/dri
```

