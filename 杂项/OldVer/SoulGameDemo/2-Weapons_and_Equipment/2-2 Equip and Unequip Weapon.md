
主要内容：将武器连接到角色手上


- 项目设置中：设置新的 **动作映射**
    - 在手部骨骼设置 [**Socket(官方文档链接)**](https://docs.unrealengine.com/4.27/zh-CN/WorkingWithContent/Types/StaticMeshes/HowTo/Sockets/)
    - 获取当前 attached 的武器
    - 将其重新 attach to actor，这次输入手部设置的 Socket Name

- 添加武器切换到手上的动画，涉及到**Animation Notify**
   - **Animation Notify**：提供创建与动画序列同步的可重复事件的方法
     - 这里涉及到的为：使附加武器在动画期间的特定点发生
     - [官方文档](https://docs.unrealengine.com/5.0/en-US/animation-notifies-in-unreal-engine/)

- 动画插槽：用于叠加并播放动画
  - [官方文档](https://docs.unrealengine.com/5.0/zh-CN/animation-slots-in-unreal-engine/)

