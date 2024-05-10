# latx-build

## 1.5.1-rc1
  * latx-x86_64 实现库直通技术，直通了包括 libgl、libx11 等 40 余个动态库；可以设置环境变量LATX_KZT=0关闭；
  * 实现 AOT (ahead of time) 技术，基于已运行代码生成静态缓存加速后续运行；可以设置环境变量LATX_AOT=0关闭；
  * spec2000 效率 65%; 
  * 支持 ssse3 和 sse4 指令集;
  * 网络相关系统调用支持更多套接字类型，支持更多 ioctl.
