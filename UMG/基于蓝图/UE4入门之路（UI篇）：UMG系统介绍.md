
[安全验证 - 知乎](https://zhuanlan.zhihu.com/p/117582084)
- 控件的部分属性、四种控件（Common、Input、Panel、UserCreated）
- 几个值得注意的：
	- 控件的**IsVariables**：勾选的变量越多，就需要越多的时间去读表，因此未引用的图层尽量不要勾选
	- UMG 的渲染顺序：Panel 中图层从下至上的层级，需要注意的是 Canvas Panel 的所有子集都是平行关系，ZOrder 的调整局限于控件的父级内部
	- 控件的 `DrawAs`，四种
		- None，不渲染，可为全透明，但保留交互效果
		- Box，**九宫拉伸**，margin 为九宫的百分比
		- Border，与 Box 类似，但中空，拉伸区域会重复以填充
		- Image，直接拉伸
	- Size 尺寸：UMG 制作时默认 DPI 为 96 像素/英寸，PSD 默认为 72 像素/英寸。在 GUI 设计时将 DPI 设为 96，或将 72 DPI 下字体的字号乘以 0.75，以还原效果图种效果。
	- 对齐方式：一般只能调整垂直方向对齐方式，因为 UE 4 的对其逻辑依靠控件与 Panel 之间的相对位置来进行，需要设置竖直方向对齐可以嵌套一层 SizeBox
	- Interaction 交互性：IsFocusable
		- 勾选后，控件接收到点击事件会打算其他所有正在进行的操作
		- 注意只给强打断性界面设置勾选
		- 技能、吃药、切换武器等需要配合其他操作的界面入口不能勾选
	- Visibility 可见性
		- Visibile，可见并接收交互事件
		- Collapsed，不可见且不占用体积
		- Hidden，不可见但占用体积，不接受交互
		- Hit Test Invisible，可见，但和子节点都不接收交互
		- Set Hit Test Visible，可见，自己不接受交互，不影响子节点
	- 优化简易：在接收到交互事件时，会遍历所有 visible 控件，所以将可见但不可交互的控件设置为 Set Hit Test Visible 可以提交计算速度，例如 Image
			


