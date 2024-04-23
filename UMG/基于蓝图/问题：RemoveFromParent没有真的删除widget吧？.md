
## 情景描述

点击离开按钮创建确认界面，点击是则关闭游戏，点击否就关闭确认界面。

![[Pasted image 20240408181825.png]]

## 调试过程

这里对按钮“否”设置断点：
![[Pasted image 20240408181959.png]]


如下图：这里多次创建确认界面并点击否，RemoveFromParent，可以看到之前创建的 Widget 仍然存在
![[RemoveFromParent_NotDeleteWidget.gif]]


## 2024-4-8 更新

当执行到 RemoveFromParent 后，Widget 不会立即删除，而是过一段时间后才会被清理。

这个现象是我在上边那个调试界面多停留了一会发现的。

