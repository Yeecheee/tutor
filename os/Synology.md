# Synology 设置和常用软件

### 开启ssh

开启root登录
vim /etc/ssh/sshd_config

PermitRootLogin yes
开启密钥登陆
AuthorizedKeysFile      .ssh/authorized_keys
PubkeyAuthentication yes

输入下面命令修改root默认密码:synouser --setpw root xxxxxx 后面的xxxxx更换成你自己的密码。



### Alist

#### 创建容器

``` YML

services:
  alist:
    container_name: alist
    image: xhofe/alist:latest
    restart: unless-stopped
    ports:
      - 27000:5244
    volumes:
      - ./data:/opt/alist/data
      - /volume2/RSS:/RSS
      - /volume2/Share:/Share
      - /volume3/Media:/Media
      - /volume3/Picture:/Picture
      - /volume3/Software:/Software
      - /volume3/Game:/Game

    environment:
      - PUID=0
      - PGID=0
      - UMASK=022
```

#### 隐藏 @eadir

在alist下：进入【管理】--【设置】--【全局】，找到隐藏文件，在下面的框内增加以下现行内容

/\/@eaDir/i

#### 自定义头部

``` HTML
<!--引入字体-->
<link rel="stylesheet" href="https://npm.elemecdn.com/lxgw-wenkai-webfont@1.1.0/lxgwwenkai-regular.css" />
<style>
/*字体霞鹭文楷*/
*{font-family:LXGW WenKai}
*{font-weight:regular}
body {font-family: LXGW WenKai;}
</style>
```


### qBittorent

``` YML

services:
  PT:
    container_name: PT
    image: linuxserver/qbittorrent
    restart: unless-stopped
    networks:
      macvlan_net:
        ipv4_address: 10.0.0.21
    volumes:
      - ./PT:/config
      - /volume2/Download:/downloads
      - /volume3/PT:/PT
    environment:
      - PUID=0
      - PGID=0
      
      - TZ=Asia/Shanghai
      - WEBUI_PORT=80
      - TORRENTING_PORT=27321


  BT:
    image: linuxserver/qbittorrent
    container_name: BT
    restart: unless-stopped
    networks:
      macvlan_net:
        ipv4_address: 10.0.0.22
    volumes:
      - ./BT:/config
      - /volume2/Download:/downloads
    environment:
      - PUID=0
      - PGID=0
      
      - TZ=Asia/Shanghai
      - WEBUI_PORT=80
      - TORRENTING_PORT=27322

  RSS:
    image: linuxserver/qbittorrent
    container_name: RSS
    restart: unless-stopped
    networks:
      macvlan_net:
        ipv4_address: 10.0.0.23
    volumes:
      - ./RSS:/config
      - /volume2/Download:/downloads
      - /volume2/RSS:/RSS
    environment:
      - PUID=0
      - PGID=0
      
      - TZ=Asia/Shanghai
      - WEBUI_PORT=80
      - TORRENTING_PORT=27323

  SEED:
    image: chisbread/transmission
    container_name: SEED
    restart: unless-stopped
    networks:
      macvlan_net:
        ipv4_address: 10.0.0.24
    volumes:
      - ./SEED:/config
      - /volume2/Download:/downloads
      - /volume3/PT:/PT
    environment:
      - PUID=0
      - PGID=0
      - USER=
      - PASS=
      - TZ=Asia/Shanghai
      - RPCPORT=80
      - PEERPORT=27324

networks:
  macvlan_net:
    driver: macvlan
    driver_opts:
      parent: eth0  # 将eth0替换为你实际的网卡名称
    ipam:
      config:
        - subnet: 10.0.0.0/24
          gateway: 10.0.0.1

```

> PUID PGID 须为 0, 否则不能使用 80 端口, 不知道为什么



### MoviePilot

#### 构建容器

``` YML

# MoviePilot 地址：https://github.com/jxxghp/MoviePilot

services:
  moviepilot:
    container_name: MoviePilot
    image: jxxghp/moviepilot:latest
    hostname: moviepilot
    tty: true
    restart: unless-stopped
    ports:
      - 27500:27500
      - 27501:27501
    volumes:
      - ./config:/config
      - ./core:/moviepilot
      - /volume1/docker/DL/PT/qBittorrent/BT_backup:/qb
      - /volume1/docker/DL/SEED/torrents:/tr
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      # WEB服务端口，默认3000，可自行修改，不能与API服务端口冲突
      - NGINX_PORT=27500
      # API服务端口，默认3001，可自行修改，不能与WEB服务端口冲突
      - PORT=27501
      # 运行程序用户的uid，默认0
      - PUID=0
      # 运行程序用户的gid，默认0
      - PGID=0
      # 掩码权限，默认000，可以考虑设置为022
      - UMASK=022
      # 时区
      - TZ=Asia/Shanghai
      # 重启时自动更新，true/release/dev/false，默认release，需要能正常连接Github 注意：如果出现网络问题可以配置PROXY_HOST
      - MOVIEPILOT_AUTO_UPDATE=true
      # 麒麟认证
      - AUTH_SITE=hdkyl
      - HDKYL_UID= # 自行修改获取
      - HDKYL_PASSKEY=
      # 超级管理员用户名，默认admin，安装后使用该用户登录后台管理界面，注意：启动一次后再次修改该值不会生效，除非删除数据库文件！
      - SUPERUSER=
      # API密钥，默认moviepilot，在媒体服务器Webhook、微信回调等地址配置中需要加上?token=该值，建议修改为复杂字符串
      - API_TOKEN=
      # 大内存模式，默认为false，开启后会增加缓存数量，占用更多的内存，但响应速度会更快
      - BIG_MEMORY_MODE=true
      # Github token，提高自动更新、插件安装等请求Github Api的限流阈值，格式：ghp_**** 或 github_pat_****
      - GITHUB_TOKEN=
      # 开发者模式，true/false，默认false，开启后会暂停所有定时任务
      - DEV=false
      # debug模式，开启后会输出debug日志
      - DEBUG=true
      # 启动时自动检测和更新资源包（站点索引及认证等），true/false，默认true，需要能正常连接Github            
      - AUTO_UPDATE_RESOURCE=true
      # TMDB API地址，默认api.themoviedb.org，也可配置为api.tmdb.org、tmdb.movie-pilot.org 或其它中转代理服务地址，能连通即可
      - TMDB_API_DOMAIN=api.themoviedb.org
      # TMDB图片地址，默认image.tmdb.org，可配置为其它中转代理以加速TMDB图片显示，如：static-mdb.v.geilijiasu.com
      - TMDB_IMAGE_DOMAIN=image.tmdb.org
      # 登录首页电影海报，tmdb/bing，默认tmdb
      - WALLPAPER=tmdb
      #  媒体信息识别来源，themoviedb/douban，默认themoviedb，使用douban时不支持二级分类
      - RECOGNIZE_SOURCE=themoviedb
      # Fanart开关，true/false，默认true，关闭后刮削的图片类型会大幅减少
      - FANART_ENABLE=true
      # 刮削元数据及图片使用的数据源，themoviedb/douban，默认themoviedb
      - SCRAP_SOURCE=themoviedb
      # 新增已入库媒体是否跟随TMDB信息变化，true/false，默认true，为false时即使TMDB信息变化了也会仍然按历史记录中已入库的信息进行刮削
      - SCRAP_FOLLOW_TMDB=true
      # 远程交互搜索时自动择优下载的用户ID（消息通知渠道的用户ID），多个用户使用,分割，设置为 all 代表全部用户自动择优下载，未设置需要手动选择资源或者回复0才自动择优下载
      - AUTO_DOWNLOAD_USER=all
      # OCR识别服务器地址，格式：http(s)://ip:port，用于识别站点验证码实现自动登录获取Cookie等，不配置默认使用内建服务器https://movie-pilot.org
      - OCR_HOST=https://movie-pilot.org
      # 下载站点字幕，true/false，默认true
      - DOWNLOAD_SUBTITLE=true
      # 电影重命名格式
      - MOVIE_RENAME_FORMAT={{title}}{% if year %} ({{year}}){% endif %}/{{title}}{% if year %} ({{year}}){% endif %}{% if part %}-{{part}}{% endif %}{% if videoFormat %} - {{videoFormat}}{% endif %}{{fileExt}}
      # 电视剧重命名格式
      - TV_RENAME_FORMAT={{title}}{% if year %} ({{year}}){% endif %}/Season {{season}}/{{title}} - {{season_episode}}{% if part %}-{{part}}{% endif %}{% if episode %} - 第 {{episode}} 集{% endif %}{{fileExt}}
      # 插件市场仓库地址，仅支持Github仓库main分支，多个地址使用,分隔
      - PLUGIN_MARKET=https://github.com/jxxghp/MoviePilot-Plugins/,https://github.com/thsrite/MoviePilot-Plugins/,https://github.com/honue/MoviePilot-Plugins/,https://github.com/InfinityPacer/MoviePilot-Plugins/,https://github.com/dandkong/MoviePilot-Plugins/,https://github.com/Aqr-K/MoviePilot-Plugins/,https://github.com/AnjoyLi/MoviePilot-Plugins/,https://github.com/WithdewHua/MoviePilot-Plugins/,https://github.com/HankunYu/MoviePilot-Plugins/,https://github.com/baozaodetudou/MoviePilot-Plugins/,https://github.com/almus2zhang/MoviePilot-Plugins/,https://github.com/Pixel-LH/MoviePilot-Plugins/,https://github.com/lightolly/MoviePilot-Plugins/,https://github.com/suraxiuxiu/MoviePilot-Plugins/,https://github.com/gxterry/MoviePilot-Plugins/

    
```


### Aria2 & AriaNg

``` YML
services:
  Aria2-Pro:
    container_name: aria2-pro
    image: p3terx/aria2-pro
    restart: unless-stopped
    ports:
      - "27100:27100"
      - "27101:27101"
      - "27101:27101/udp"
    volumes:
      - ./aria2/config:/config
      - /volume2/Download:/downloads
    environment:
      - UMASK_SET=022
      - RPC_SECRET=
      - RPC_PORT=27100
      - LISTEN_PORT=27101
      - UPDATE_TRACKERS=true
      - TZ=Asia/Shanghai

  AriaNg:
    container_name: ariang
    image: p3terx/ariang:latest
    restart: unless-stopped
    ports:
      - "27110:6880"
```

