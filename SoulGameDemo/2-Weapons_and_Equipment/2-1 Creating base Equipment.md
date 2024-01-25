

这一节的主要内容：
1. 创建可穿戴武器类的基类与派生类：
   - Base Equippable actor class
   - Base Weapon actor class
       - Example Weapon 1
       - Example Weapon 2
       - Example Weapon 3
2. Base Equippable actor class 
   - 设置装备/卸下的功能函数
   - 附加到模型上的功能函数
3. 将武器附加到人物模型上
   - Socket
   - collision off and no can step on




其他：
- 关于视频中的 pure function，这里是[官方文档链接：函数调用](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/Blueprints/UserGuide/Functions/FunctionCalls/)
    - 有些类似 c 中的 const 函数
    - pure function 不会改变游戏中的其他变量（文档中的说法是：不会直接影响世界或者世界中的对象）

- Instigator：造成伤害的执行主体
  - [官方文档链接：Instigator](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Engine/Engine/FActorSpawnParameters/Instigator/)
  - 例如：坦克开炮命中敌人，坦克是炮弹的 Owner，而按下开炮按钮导致造成伤害的人就是这个 Instigator。参考自：[SpawnActor 的 Owner 和 Instigator 参数](https://blog.csdn.net/wangchao701123/article/details/120988921)



