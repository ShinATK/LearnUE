#cg #ue4 
- [【蓝图】02-1课：按键+鼠标点击实现开关门\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV164411Y732?t=44.3&p=28)
	- 创建门的蓝图类
		- Actor 和组件的区别、门的轴心点修改
	- 相对位置和世界位置
		- 设置 Actor 旋转、设置世界旋转、设置相对旋转的区别
	- 按 E 键开关门
	- 鼠标点击开关门
		- 创建一个新的 player controller 类
---
# 实现按 E 键开关门
## 步骤一：建立新的蓝图类Actor Door_BP

- 内容浏览器空白处右键
	- 蓝图->蓝图类->Actor
![](img/Pasted%20image%2020240110211202.png)
![](img/Pasted%20image%2020240110211239.png)

- 双击打开新建立的蓝图类

## 步骤二：设置门框、门和碰撞盒

![](img/Pasted%20image%2020240110211337.png)

- 建立如图所示层级关系：![](img/Pasted%20image%2020240110211416.png)

**注意**：这里的碰撞检测盒要与 Door 放在同一级，防止 Box 随着 Door 的打开而改变了位置

- 为静态网格体添加模型
	- DoorFrame 添加门框模型
	- Door 添加门模型
![](img/Pasted%20image%2020240110211534.png)

## 步骤三：设置蓝图

- 切换页面到**事件图表**
![](img/Pasted%20image%2020240110211645.png)

大体上和 01 中设置相同

- 设置开始重叠检测和结束重叠检测
	- 可以右键添加
	- 也可以在左侧组件中选中碰撞盒，然后在右侧细节面板处找到
![](img/Pasted%20image%2020240110211820.png)

- 添加时间轴：timeline，同 [01 自动开关门互动](../蓝图%2001%20自动开关门互动/01%20自动开关门互动.md)
- 在组件中选中 Door，右键添加 relative roate（注意红框中内容）
![](img/Pasted%20image%2020240110212204.png)

- 链接节点，目前可以得到一个简单的开关门蓝图

![](img/Pasted%20image%2020240110212302.png)

此时效果和 [01 自动开关门互动](../蓝图%2001%20自动开关门互动/01%20自动开关门互动.md)中相同

## 步骤四：添加键盘 E 键开门

- 添加获取键盘输入，如图
![](img/Pasted%20image%2020240110212457.png)

- 记得设置*启用输入 enable input*和*禁用输入 disable input*，如图

![](img/Pasted%20image%2020240110212613.png)

- 添加 gate，用以流程控制
![](img/Pasted%20image%2020240110212702.png)

- 添加 FlipFlop 触发器以实现正向和反向信号
![](img/Pasted%20image%2020240110212837.png)
- 此时还需要获取*Player Controller*，需要知道玩家此时的控制器是什么
	- 连接到 Enable Input 和 Disable Input 的 PlayerController 处
![](img/Pasted%20image%2020240110212938.png)

最终蓝图如下：![](img/Pasted%20image%2020240110213149.png)

## 最终效果

![按键开关门](img/按键开关门.gif)

# 实现鼠标点击模型开关门

## 步骤五：为 Door 模型添加一个 OnClicked 事件

- 组件面板选中 Door
- 在细节面板找到事件中 OnClicked
- 替代键盘 E 进行连接
![](img/Pasted%20image%2020240110213822.png)

## 步骤六：设置 GameMode

- 回到主窗口
- 在**世界设置**面板中找到 GameMode，设置PlayerControllerClass
![](img/Pasted%20image%2020240110213959.png)

- 在打开的蓝图类中，细节面板，鼠标接口
- 勾选“显示鼠标光标”和“启用点击事件”
![](img/Pasted%20image%2020240110214109.png)

## 目前效果

![鼠标点击开关门](img/鼠标点击开关门.gif)

## 步骤七：设置进入范围后自动显示鼠标

- “set show mouse cursor”
- 连接到如下位置
- **注意**
	- 开始重叠：设置显示鼠标光标
	- 结束重叠：取消勾选
	- *同时记得回到之前设置的 player controller 类中关闭掉设置的开启鼠标光标*
![](img/Pasted%20image%2020240110214650.png)

## 最终效果

![鼠标点击开关门-接近后触发](img/鼠标点击开关门-接近后触发.gif)

