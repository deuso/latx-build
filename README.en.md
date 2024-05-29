LATX
===

LATX (Loongson Architecture Translator for x86) is a user-mode emulator for running 32/64-bit x86 applications on Loongson (LoongArch) systems. LATX leverages technologies such as LoongArch SIMD extensions (such as vector and binary translation extensions), AOT (ahead-of-time) compiler, and native library passthrough to optimize x86 emulation performance.

When paired with [Wine](https://winehq.org), LATX can also be used to run x86 Windows applications.

Installation
---

Most Linux distributions for LoongArch supply LATX in their software repositories. If your distribution does not supply LATX, you may also install LATX manually.

### Install from Software Repositories

Loongnix, Deepin, Kylin, UOS, etc.:

```
sudo apt install lat i386-runtime-base i386-runtime-extra
```

Arch Linux (Loong Arch Linux):

```
sudo pacman -S lat lat-runtime-i386 lat-runtime-x86_64
```

AOSC OS:

```
oma install latx
```

### Manual Installation

LATX comes with two main executables:

- `latx-x86_64` for running 64-bit x86 applications.
- `latx-i386` for running 32-bit x86 applications.

After installing the main executables (distribution vendors should use `/usr/bin`, personal/local installations should use `/usr/local/bin`), you would also need to prepare the x86 runtime environments (usually a distribution sysroot, adjust in accordance to your own needs):

- `/usr/gnemul/lat-x86_64` contains the 64-bit x86 runtime environment.
- `/usr/gnemul/lat-i386` contains the 32-bit x86 runtime environment.

LATX is now ready to go.

Usage
---

Once LATX is installed, you may run your x86 applications via the main executables `latx-x86_64` and `latx-i386`. For instance:

```
latx-x86_64 ~/x86/x86_64-linux-gnu-gcc
```

Environmental Variables
---

LATX uses environmental variables to toggle its various internal features. You may turn on or off these features, should you run into compatibility or stability issues:

- `$LATX_AOT` enables/disables ahead-of-time (AOT) compiler (caching instructions and data to improve performance); specify 1 to enable, 0 to disable.
- `$LATX_KZT` enables/disables native library passthrough (using native libraries where applicable to improve performance); specify 1 to enable, 0 to disable.

For debugging only:

- `$LATX_SOFTFPU` enables/disables soft float emulation (to guarantee the 80-bit precision of x87 instructions); specify 1 to enable, 0 to disable.

Typically, 32-bit applications are more dependent upon the precision of the x87 floating-point unit. Should you encounter issue(s) with your application, you may try and enable `$LATX_SOFTFPU` to see if it fixes your issue(s).

Configuring binfmt_misc
---

It is recommended to configure the Linux Kernel's binfmt_misc functionality for LATX. This allows the kernel to recognize and run x86 binaries via LATX transparently (the user may run x86 applications directly; in graphical environments, this also allows applications to be launched normally via double-clicks, shortcuts, and icons).

Below are sample binfmt_misc configurations for LATX.

### 64-bit x86 Applications

For Linux distributions using systemd, it is recommended to install the following configure to `/usr/lib/binfmt.d/latx-x86_64.conf`. After which, run `systemctl restart systemd-binfmt` to allow the configuration to take effect.

```
:x86_64:M::\x7fELF\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x3e\x00:\xff\xff\xff\xff\xff\xfe\xfe\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/latx-x86_64:POCF
```

### 32-bit x86 Applications

For Linux distributions using systemd, it is recommended to install the following configure to `/usr/lib/binfmt.d/latx-i386.conf`. After which, run `systemctl restart systemd-binfmt` to allow the configuration to take effect.

```
:i386:M::\x7fELF\x01\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x03\x00:\xff\xff\xff\xff\xff\xfe\xfe\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/latx-i386:POCF
```
