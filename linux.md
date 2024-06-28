apt install duf




删除欢迎提示


/etc/update-motd.d
/etc/motd
/etc/issue


删除lastlogin消息
touch ~/.hushlogin
恢复lastlogin消息
rm ~/.hushlogin



oh-my-zsh
更新软件源
apt update && apt upgrade -y
安装 zsh git curl
apt install zsh git curl -y
安装oh-my-zsh
sh -c "$(curl -fsSL https://install.ohmyz.sh/)"
chsh -s /bin/zsh

安装powerlevel10k主题
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k

p10k 重新配置
p10k configure




git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
vim ~/.zshrc
plugins=(
     git
     zsh-syntax-highlighting
     zsh-autosuggestions
)


docker


curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh





neovim

curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux64.tar.gz


copy to ~/.zshrc or ~/.bashrc
export PATH="$PATH:/opt/nvim-linux64/bin"

or

sudo ln -s /opt/nvim/bin/nvim /usr/bin/nvim





nvimdots/neovim

sudo apt install git unzip make cmake gcc g++ clang zoxide ripgrep fd-find yarn lldb python3-pip  python3-venv
# lazygit
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit /usr/local/bin
# nvm
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
nvm install 18
nvm use 18
# cargo/rustc required by sniprun and rustfmt
curl https://sh.rustup.rs -sSf | sh



fastfetch 
repo地址
https://github.com/fastfetch-cli/fastfetch/releases
ssh登录启动
sed -n 'w ~/.fastfetch' <<< 'fastfetch'
sed -i '1isource ~/.fastfetch' ~/.zshrc

or

if [[ $- == *i* ]]; then
    # 执行 ls 指令
    fastfetch
fi