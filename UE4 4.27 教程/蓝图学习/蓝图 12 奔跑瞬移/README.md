#gamedev  #ue4 

---

# 奔跑

**实现功能**：按住 Shift 快速奔跑，松开 Shift 速度恢复正常

**基本逻辑**：
- 检测 shift 按键
	- pressed
		- set walk speed 2000
	- released
		- set walk speed default

![](img/Pasted%20image%2020240117233007.png)

# 瞬移

**实现功能**：快按两下 `Left Ctrl` 进行瞬移

**基本逻辑**
- 检测 `Ctrl` 输入次数，等于 2 则移动到前方某个位置

## 检测输入次数

![](img/Pasted%20image%2020240117233210.png)

计数会一直增加下去，导致后续输入失效

## 重置输入次数

![](img/Pasted%20image%2020240117233230.png)

- 增加 Delay 节点
- 当检测到 ctrl 被释放，则延迟 0.2 s 将计数置 0；触发瞬移必须在 0.2 s 内连续输入两次

## 实现瞬移

**基本思路**
- 获取 actor 的 location 与 forward vector
- location += 倍率 * foward vector 实现向前瞬移

![](img/Pasted%20image%2020240117233523.png)

利用 timeline 实现瞬移位置的过渡

## 设置镜头变化

为凸显瞬移，可以设置镜头的 fov 变化

![](img/Pasted%20image%2020240117233623.png)

触发瞬移时，fov 设置为 50，瞬移的 0.2s 内恢复为 90

## 最终效果

![player_dash](img/player_dash.gif)


