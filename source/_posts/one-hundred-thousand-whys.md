---
title: 十万个为什么
date: 2025-06-13 09:57:10
tags:
---

## 1. ssh 到远程机器上，打开 tmux，如果此时网络断开，为什么终端会把鼠标滚轮解释错误？

现象1：上滚轮会`64;133;44M`，下滚轮会输出`65;133;44M`，进一步观察到，第一个数字只和鼠标滚轮方向有关，向上是`64`，向下是`65`，而后面两个数字，则和鼠标所在位置有一定的关系，第2个数字，是光标在 x 轴上的位置，第3个数字，是光标在y轴上的位置。再进一步观察，第一个数字和鼠标操作有关，鼠标左键是`0`，鼠标右键是`2`，鼠标中键是`1`，鼠标拖动是`32`，前进后退键无反应。

现象2：使用 reset 命令后不再解释错误

回答：结合最后的`M`，可以判断，这应该是终端所捕获的鼠标命令，第一个数字代表鼠标操作类型，第二个数字是鼠标横轴位置，第二个数字是鼠标纵轴位置，可是这

现象2：使用 reset 命令后不再解释错误。

回答：reset 重置了虚拟终端的状态，使其不再相应鼠标事件。可以通过这样一个简单的操作[^1][^2]来启用鼠标追踪，复现上面的问题：

```bash
echo -e "\e[?1000;1006;1015h"
```

## 2. 复制 tmux 的内容时，有时会产生

[^1] [How to configure the terminal so that a mouse click will move the cursor to the current mouse position - StackExchange](https://unix.stackexchange.com/questions/35021#509695)
[^2] [how to get MouseMove and MouseClick in bash?](https://stackoverflow.com/questions/5966903)

参考资料

- man read(1) https://man7.org/linux/man-pages/man1/read.1p.html
- man reset(1) https://linux.die.net/man/1/reset
- XTERM Console Codes https://tintin.mudhalla.net/info/xterm/
- how to get MouseMove and MouseClick in bash? https://stackoverflow.com/questions/5966903
- What are the button codes for mouse scroll wheel left/right, in DECSET SGR 1006? https://stackoverflow.com/questions/51578176
- How to configure the terminal so that a mouse click will move the cursor to the current mouse position https://unix.stackexchange.com/questions/35021
- Complex mouse inputs in XTerm https://unix.stackexchange.com/questions/518479
- VT220 https://en.wikipedia.org/wiki/VT220
- XTerm Control Sequences https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h2-Mouse-Tracking
- XTerm Control Sequences https://invisible-island.net/xterm/ctlseqs/ctlseqs.pdf
-
-



