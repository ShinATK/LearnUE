
> UE 4.27 源码版本，VS 2022 17.9.6

---
# 前置知识

- [状态机是怎么个回事](../../学习笔记/状态机/README.md)
- [游戏里的状态模式是怎么个回事](../../学习笔记/《游戏设计模式》/状态模式/README.md)


---
# 状态机与 UI

# 基于帧状态机的框架实现

`StackStateInterface.h`

- DECLARE_LOG_CATEGORY_EXTERN(LogStateMachine, Log, All);
- DECLARE_DYNAMIC_MULTICAST_DELEGATE(FOnStateEnterSignature);
- UINTERFACE(MinimalAPI)
- UINTERFACE(meta = (CannotImplementInterfaceInBlueprint))
- 接口的定义方式


`StackState.h`

- UCLASS(Abstract, BlueprintType, Blueprintable, HidCategories=("Cokking", "LOD", "Physics", "Activation", "Tags", "Rendering"))
- 重载接口
- 蓝图回调

`StackStateMachineComponent.h`
- DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnStackStatePushPopSignature, TScripInterface<IStackStateInterface>, State);
- 


---
# 参考链接
- [用状态机处理复杂的 UI 状态](https://blog.icyh.me/component-design/state-machine/)
- [UE4 基于栈状态机的 UI 管理 (框架设计)](https://zhuanlan.zhihu.com/p/143882791)
