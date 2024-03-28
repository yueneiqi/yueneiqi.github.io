---
layout: post
title: "MacOS通过QEMU启动openEuler RISC-V"
date: 2024-03-25
author: 玉树
categories: [Linux]
tags: [Linux, RISC-V ]
comments: true
toc: true
---

### 安装qemu

安装依赖：

```bash
brew install gawk gnu-sed gmp mpfr libmpc isl zlib expat
```

安装qemu：

```bash
brew install qemu
```

### 下载openEuler的镜像

从[网站](https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/preview/)中选择版本，这里我选择了`openEuler-23.09-V1-riscv64`[(网址)](https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/preview/openEuler-23.09-V1-riscv64/QEMU/)

如果想要桌面系统，记得下载带有xfce的

保证当前目录下有一个启动脚本.sh，一个bin文件和一个zst文件，解压缩zst文件，并将sh文件和bin文件和解压缩后的qcow2文件放在一个文件夹中

### 启动openEuler

执行脚本启动：

```bash
 bash start_vm_xfce.sh
```

执行后发现脚本中两处参数失效，查看`qemu-system-riscv64 --help`和`qemu-system-riscv64 -audiodev help`的相关信息后，修改参数为：

```bash
...
  -display cocoa,show-cursor=on \
  -audiodev coreaudio,id=snd0 \
```

启动后，默认的用户`openeuler`，密码是`openEuler12#$`

![success](https://s2.loli.net/2024/03/25/kNr78lTaGpe23ED.png)

## 参考

1. [oerv-pretask（一）通过 QEMU 仿真 RISC-V 环境并启动 openEuler RISC-V 系统，设法输出 neofetch 结果并截图提交 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/675382611)
2. [Running 64- and 32-bit RISC-V Linux on QEMU — RISC-V - Getting Started Guide (risc-v-getting-started-guide.readthedocs.io)](https://risc-v-getting-started-guide.readthedocs.io/en/latest/linux-qemu.html)
