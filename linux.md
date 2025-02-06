# Linux 下系统设置和常用软件

## 系统设置

### 删除欢迎提示

``` Shell
vim /etc/update-motd.d
vim /etc/motd
vim /etc/issue
```

#### 删除lastlogin消息  

``` Shell
touch ~/.hushlogin
```

#### 恢复lastlogin消息  

``` Shell
rm ~/.hushlogin
```

## 常用软件

### duf -- 图形化 df 指令

``` Shell
apt install duf
```

### htop -- 图形化 top 工具

### oh-my-zsh

<p align="center">
    <a href="https://ohmyz.sh/">官网</a>
    |
    <a href="https://github.com/ohmyzsh/ohmyzsh">GitHub</a>
    |
    <a href="https://hanmeimei222.gitbooks.io/tools/content/qiang-hua-ni-de-iterm2/oh-my-zsh/oh-my-zsh-chang-yong-she-zhi.html">中文 GitBook</a>
</p>

#### 用途

zsh 优化

#### 步骤

1. 更新软件源

    ``` Shell
    apt update && apt upgrade -y  
    ```

2. 安装 zsh git curl

    ``` Shell
    apt install zsh git curl -y
    ```

3. 安装 oh-my-zsh

    ``` Shell
    sh -c "$(curl -fsSL https://install.ohmyz.sh/)"
    ```

4. 切换当前用户 Shell 为 zsh

    ``` Shell
    chsh -s /bin/zsh
    ```

5. 设置主题  
    默认安装后，使用的主题为 `robbyrussell` ，可以在这里找到[各种主题](https://github.com/ohmyzsh/ohmyzsh/wiki/themes)。  
    以下为设置主题步骤：  
    1. 找到喜欢的主题
       - `~/.oh-my-zsh/themes` 目录下已经内置很多主题，可以选择其中一种；
       - 或者自定义主题：找到一款喜欢的主题，把主题文件下载到 `~/.oh-my-zsh/themes` 里面。
    2. 修改 `~/.zshrc` 文件，配置好主题名字即可，以 `suvashi` 主题为例

        ```VIM
        ZSH_THEME="suvashi"
        ```

6. 安装powerlevel10k主题
   git clone --depth=1 <https://github.com/romkatv/powerlevel10k.git> $ZSH_CUSTOM/themes/powerlevel10k

p10k 重新配置
p10k configure

git clone <https://github.com/zsh-users/zsh-autosuggestions> ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone <https://github.com/zsh-users/zsh-syntax-highlighting.git> ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
vim ~/.zshrc
plugins=(
     git
     zsh-syntax-highlighting
     zsh-autosuggestions
)

### docker

``` Shell
curl -fsSL <https://get.docker.com> -o get-docker.sh
sh get-docker.sh
```

### neovim

```Shell
curl -LO <https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz>
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux64.tar.gz

copy to ~/.zshrc or ~/.bashrc
export PATH="$PATH:/opt/nvim-linux64/bin"

or

sudo ln -s /opt/nvim/bin/nvim /usr/bin/nvim
```

### nvimdots/neovim

``` Shell
sudo apt install git unzip make cmake gcc g++ clang zoxide ripgrep fd-find yarn lldb python3-pip  python3-venv
```

### lazygit

LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit /usr/local/bin

### nvm

curl <https://raw.githubusercontent.com/creationix/nvm/master/install.sh> | bash
nvm install 18
nvm use 18

### cargo/rustc required by sniprun and rustfmt

curl <https://sh.rustup.rs> -sSf | sh

### fastfetch

<p align="center">
  <a href="https://github.com/fastfetch-cli/fastfetch">GitHub</a>
</p>

#### ssh 登录启动

``` Shell
sed -n 'w ~/.fastfetch' <<< 'fastfetch'
sed -i '1isource ~/.fastfetch' ~/.zshrc
```

### Shell-GPT  

#### 安装  

``` Shell
pip install shell_gpt
pipx install shell_gpt
```

修改配置文件

``` Shell
vim ~/.config/shell_gpt/.sgptrc
```

> 其中地址需要在后面加上 `/v1`

### ACME

``` Shell
https://www.panyanbin.com/article/c44653d8.html
curl  https://get.acme.sh | sh
```

#### 修改配置文件

``` Shell
vim /etc/nginx/nginx.conf
```

``` CONF
ssl_certificate             /etc/letsencrypt/live/example.com/privkey.pem;
ssl_certificate_key         /etc/letsencrypt/live/example.com/privkey.pem;
ssl_protocols               TLSv1.2 TLSv1.3;
ssl_ciphers                 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256';
ssl_prefer_server_ciphers   on;
```

### open-vm-tools

https://blog.csdn.net/lxyoucan/article/details/124280344

### lrzsz
