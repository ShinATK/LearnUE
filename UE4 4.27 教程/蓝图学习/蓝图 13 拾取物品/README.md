
- 射线检测
- 碰撞设置
- 蓝图接口

## 资产导入

枪械模型：
![](img/Pasted%20image%2020240119225705.png)

模型贴图：
![](img/Pasted%20image%2020240119225726.png)

**注意到**，枪械模型的文件中应该将最内层的 `Content` 下的 `WeaponProM4A1` 文件夹放到 UE 的工程文件夹当中

## 创建骨骼网格体

- 首先按照正常流程创建一个蓝图类
- 在导入的模型资产选中对应的骨骼网格体

![](img/Pasted%20image%2020240119231358.png)
- 在添加 `Component` 中会自动出现对应的骨骼网格体

![](img/Pasted%20image%2020240119231328.png)

## 从相机生成射线

需要使用到的节点：**LineTraceByChannel**，[文档链接](https://docs.unrealengine.com/4.26/en-US/BlueprintAPI/Collision/LineTraceByChannel/)

| LineTraceByChannel | 展开 OutHit 输出后 |
| ---- | ---- |
| ![](img/Pasted%20image%2020240119225902.png) <br> | ![](img/Pasted%20image%2020240119230114.png) <br> |

该节点的作用是：从 `start` 到 `end` 生成一条射线，并获取在对应的 `TraceChannel` 发生碰撞位置（交点位置）的信息，参考展开的 `Out Hit` 信息
- 这里需要的是交点所在的 `Actor` 信息

由于是想要制作从视线发出的一条射线与场景中物体的交互，所以：
- `start` 和 `end` 利用角色相机的 `GetWorldLocation` 和 `GetForwardVector` 节点来设置

在 `DrawDebugType` 中选择 `Duration`，使生成的射线延迟消失以供 debug 使用


## 自定义碰撞 Channel

由于是为了实现拾取功能而生成的射线，为了不与其他的 `collision` 发生冲突，这里需要自定义一个碰撞的 Channel

- 在关卡界面上方，选择 `Settings->Project Settings`

![](img/Pasted%20image%2020240119230939.png)

- 在下图所示界面，找到 `Engine->Collision`
	- 建立新的 `Trace Channel`
	- **记得将默认的 Response 设置为 Ignore**，目的是保证其他非拾取物品的该 channel 不会处于默认开始的状态

![](img/Pasted%20image%2020240119231040.png)

同时别忘了在对应的要设置拾取的物品细节面板中的 collision 开启该通道的检测

![](img/Pasted%20image%2020240119232358.png)


## 添加 PickUp 蓝图接口

蓝图接口的官方文档链接：[Blueprint Interface | Unreal Engine Documentation](https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/Blueprints/UserGuide/Types/Interface/)

- 在之前新建立的骨骼网格体的位置，新建立 `Blueprint Interface` 用以建立蓝图接口
- 在蓝图接口中右侧添加三个函数，分别为
	- `PickUp`
	- `CustomDepthOpen`
	- `CustomDepthClose`
- 回到用以拾取的骨骼网格体，打开蓝图，在如下位置添加新建的蓝图接口

![](img/Pasted%20image%2020240119231853.png)

- 此时便可以在蓝图中以**添加事件**的形式加入该接口
	- 目的是接收射线碰撞的信息

![](img/Pasted%20image%2020240119231950.png)

- 同时要在角色的生成射线之后利用 `OutHit` 中的 `Actor` 调用 `PickUp` 接口

![](img/Pasted%20image%2020240119232130.png)

## 骨骼网格体添加碰撞

虽然此时已经给角色和拾取物品都接入了 PickUp 接口，但此时目标物体是无法与射线产生碰撞的，所以需要对**骨骼网格体设置碰撞**（与静态网格体添加碰撞方式不同）

- 右键骨骼网格体，选择创建物理资产，这里先选用默认

![](img/Pasted%20image%2020240119232239.png)

- 调整碰撞胶囊体的大小

![](img/Pasted%20image%2020240119232759.png)

- 给骨骼网格体添加该物理资产

![](img/Pasted%20image%2020240119232852.png)


## 设置碰撞 channel 观察最后结果

至此已经基本完成了视线拾取检测的目标，但别忘了在对应的 `LineTrace` 中的 Channel 要选中我们自定义的`PickUp`channel

![](img/Pasted%20image%2020240119233036.png)

最终效果：

![rayCasting_4_pickUp](img/rayCasting_4_pickUp.gif)



