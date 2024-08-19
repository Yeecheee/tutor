# Windows 下系统设置和常用软件

---

## 系统设置

### Window webdav

https://www.lazychan.com/archives/kai-qi-windows-de-webdav-zhi-chi-bing-jie-jue-wu-fa-fang-wen

### 跳过联网登录

1. 按 `shift` + `F10` 开启终端
2. 输入下面指令跳过联网登录

   ``` Batch
   oobe\bypassnro
   ```

### 隐藏 CMD 的欢迎信息

启动时加上 `\k` 参数

### 在 Windows 系统中启用长路径功能

> 教程来自 [VCB-Studio > 我们爱科普 > 在 Windows 系统中启用长路径功能](https://vcb-s.com/archives/18054)

#### 步骤

1. 通过 `PowerShell` 进行设置
   1. 使用管理员模式启动 `PowerShell`
   2. 执行以下命令

      ``` Powershell
      New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" `
      -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force
      ```

   3. 重启计算机

2. 通过注册表文件 `.reg` 进行设置
   1. 新建一个 `.txt` 文本文件
   2. 将以下命令复制到文件中

      ``` Regedit
      Windows Registry Editor Version 5.00

      [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem]
      "LongPathsEnabled"=dword:00000001
      ```

   3. 将文件后缀名改为 `.reg`
   4. 双击 `.reg` 文件执行
   5. 重启计算机

3. 通过组策略编辑器进行设置 **(需要 Windows 专业版)**
   1. 使用 `Windows` + `R` 组合键调出 运行 窗口，输入 `gpedit.msc` 启动组策略编辑器
   2. 按以下路径选择配置项，开启长路径功能  
      - 英文路径:  
      `Computer Configuration` > `Administrative Templates` > `System` > `Filesystem` > `Enable Win32 long paths`  
      - 中文路径:  
      `计算机配置` > `管理模板` > `系统` > `文件系统` > `启用 Win32 长路径`
   3. 重启计算机

---


## 常用软件

### clink

1. 使用 `winget` 安装

   ``` Batch
   winget install clink
   ```

2. 去除 `clink` 欢迎信息

   ``` Batch
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

   3. 在 [oh-my-posh 主题](https://ohmyposh.dev/docs/themes) 可以预览并挑选喜欢的主题，挑好记住主题名字，后面需要使用

   4. 进入 `clink` 安装目录，创建 `oh-my-posh.lua` 文件，并编辑添加以下内容

      ```lua
      load(io.popen('oh-my-posh --config="C:\\Users\\Yeechee\\AppData\\Local\\Programs\\oh-my-posh\\themes\\quick-term.omp.json" init cmd'):read("*a"))()
      ```

      > `config` 的值为 `oh-my-posh` 的安装位置，可以在 `系统` -> `高级设置` -> `环境变量` 中找到变量 `POSH_THEMES_PATH`  
      > `quick-term` 是选择的配色方案，使用前面记住的主题名字替换

4. 为 `powershell` / `pwsh` 配置

   1. 使用 `notepad` 编辑 `powershell` / `pwsh` 配置文件  

      ``` PS1
      notepad $PROFILE
      ```

   2. 写入以下内容

      ``` PS1
      oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/quick-term.omp.json" | Invoke-Expression  
      ```

### vscode

#### 修改字体

   1. 使用 `ctrl` + `,` 调出设置面板，在搜索框中搜索 `Editor: Font Family` ，输入  

      ``` setting
      'FiraCode Nerd Font','霞鹜文楷等宽 屏幕阅读版'
      ```

      > 由于第一个字体不支持中文，`vscode` 会自动向后选择字体来支持中文，由此实现中英文不同字体 :-)
      > 2024.07.24 已改用自维护的 `Yeechee_LXGW_Mono`

### One Commander

### mpv shim jellyfin

### embyToLocalPlayer

### FontCreator

### Edge 多线程下载

### IDM

下载 > 选项 > 自定义浏览器中的IDM下载浮动条 > 对于选定的文件 -> 针对选中的链接不显示下载浮动条
下载 > 选项 > 自定义浏览器中的IDM下载浮动条 > 对于网页播放器 -> 全部清除