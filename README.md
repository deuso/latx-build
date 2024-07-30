LATX 自述
===

[See the English Version Here >>](README.en.md)

LATX（全称 Loongson Architecture Translator for x86，即“龙芯 x86 架构转译器”）是用于在龙芯（龙架构）系统上运行 32/64 位 x86 应用程序的用户态模拟器。该转译器利用龙架构的各指令集扩展（如向量扩展和二进制转译指令集）、AOT（预编译）和运行时库直通等手段加速 x86 应用程序的运行速度。

搭配 [Wine](https://winehq.org) 使用时，LATX 亦可运行为微软 Windows 设计发行的各类 x86 应用程序。

安装 LATX
---

一般来说，龙架构 Linux 发行版均有提供 LATX，可通过发行版仓库安装；如没有，亦可手动安装。

### 通过发行版仓库安装

Loongnix、Deepin、麒麟和统信等发行版：

```
sudo apt install lat i386-runtime-base i386-runtime-extra
```

Arch Linux (Loong Arch Linux)：

```
sudo pacman -S lat lat-runtime-i386 lat-runtime-x86_64
```

安同 OS (AOSC OS)：

```
oma install latx
```

### 手动安装

LATX 包含如下两个主程序：

- `latx-x86_64`：用于运行 64 位 x86 应用程序
- `latx-i386`：用于运行 32 位 x86 应用程序

将程序安装到可执行目录后（发行版放置到 `/usr/bin`，个人用户放置到 `/usr/local/bin`，还需准备 x86 运行时环境（一般为发行版系统包，内容请根据个人需要进行配置）：

- `/usr/gnemul/lat-x86_64`：64 位 x86 运行时环境
- `/usr/gnemul/lat-i386`：32 位 x86 运行时环境

一切就绪后即可使用

使用方式
---

安装 LATX 后，使用主程序 `latx-x86_64` 和 `latx-i386` 即可运行 x86 应用程序，如下例：

```
latx-x86_64 ~/x86/x86_64-linux-gnu-gcc
```

环境变量
---

LATX 使用环境变量控制其部分功能，如果您在使用时发现兼容性和稳定性问题，可通过环境变量开关部分功能：

- `$LATX_AOT`：用于控制 LATX 的预编译功能（缓存部分指令和数据以提高性能）开关。取值 1（开，默认）或 0（关）
- `$LATX_KZT`：用于控制 LATX 的运行时库直通功能（直接使用部分原生运行时以提高性能）开关。取值 1（开，默认）或 0（关）
- `$LATX_ANONYM`：使 LATX 正确处理虚拟机检测。取值 1（开）或 0（关，默认）

仅用于调试：

- `$LATX_SOFTFPU`：用于控制 LATX 使用软件浮点功能模拟功能（以保证 x87 浮点的 80 位精度），打开该功能时，使用 x87 浮点指令的程序性能会大幅下降。取值 1（开）或 0（关，默认）。

一般情况下，32 位应用程序更容易受 x87 浮点精度影响。如发现应用程序功能异常，可尝试使用该环境变量进行调试。

配置 binfmt_misc 功能
---

一般来说，推荐为 LATX 配置内核 binfmt_misc 功能，以便内核识别 x86 架构二进制并以相对透明的方式运行（用户直接运行 x86 程序命令即可利用 LATX 模拟运行；在图形界面亦可直接双击或通过图标启动）。

下为 binfmt_misc 配置样例：

### 64 位 x86 应用程序

使用 systemd 的发行版推荐将下述内容放置于 `/usr/lib/binfmt.d/latx-x86_64.conf` 中，配置完成后运行 `systemctl restart systemd-binfmt` 即可使配置生效：

```
:x86_64:M::\x7fELF\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x3e\x00:\xff\xff\xff\xff\xff\xfe\xfe\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/latx-x86_64:POCF
```

### 32 位 x86 应用程序

使用 systemd 的发行版推荐将下述内容放置于 `/usr/lib/binfmt.d/latx-i386.conf` 中，配置完成后运行 `systemctl restart systemd-binfmt` 即可使配置生效：

```
:i386:M::\x7fELF\x01\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x03\x00:\xff\xff\xff\xff\xff\xfe\xfe\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/latx-i386:POCF
```
