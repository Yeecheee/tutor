# Windows 下系统设置和常用软件

---

## 系统设置

### 跳过联网登录

1. 按 `shift` + `F12` 开启终端
2. 输入下面指令跳过联网登录

   ```Batch
   oobe\bypassnro
   ```

### 隐藏 CMD 的欢迎信息

   ```Batch
   cmd \k
   ```

---

## 常用软件

### clink

1. 使用 `winget` 安装

   ```Batch
   winget install clink
   ```

2. 去除 `clink` 欢迎信息

   ```Batch
   clink autorun install -- --quiet
   ```

### oh-my-posh

1. 使用 `winget` 安装

   ```Batch
   winget install oh-my-posh
   ```

2. 安装字体  
   [Nerd Font](https://www.nerdfonts.com/font-downloads) 网站，下载字体后安装

3. 为 `cmd` 配置

   1. 依赖 `clink` ，请先检查是否安装 `clink`
   2. 输入以下指令，在 `state` 一栏可以得到 `clink` 安装目录  

      ```Batch
      clink info
      ```

   3. 进入 `clink` 安装目录，创建 `oh-my-posh.lua` 文件，并编辑添加以下内容

      ```lua
      load(io.popen('oh-my-posh --config="C:\\Users\\Yeechee\\AppData\\Local\\Programs\\oh-my-posh\\themes\\quick-term.omp.json" init cmd'):read("*a"))()
      ```

      > `config` 的值为 `oh-my-posh` 的安装位置，可以在 `系统` -> `高级设置` -> `环境变量` 中找到变量 `POSH_THEMES_PATH`  
      > `quick-term.omp.json` 是选择的配色方案

4. 为 `powershell` / `pwsh` 配置

   1. 使用 `notepad` 编辑 `powershell` / `pwsh` 配置文件  

      ```PS1
      notepad $PROFILE
      ```

   2. 写入以下内容

      ```PS1
      oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/quick-term.omp.json" | Invoke-Expression  
      ```

