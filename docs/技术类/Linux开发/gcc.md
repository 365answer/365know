---
title: GCC编译器
tags: [GCC]
---

## 1. 编译器使用的头/库文件目录

```bash
echo 'main() {}' | gcc -E -v -
```

```bash
echo 'main() {}' | arm-none-linux-gnueabihf-gcc -E -v - 
```