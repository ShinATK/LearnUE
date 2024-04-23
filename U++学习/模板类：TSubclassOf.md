
[官方文档链接](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/UnrealArchitecture/TSubclassOf/)


**TSubclassOf**给 UClass 提供了一个类型安全的模板类

假如我们设计了一个`UDamageType`的类，用来表示游戏中所能产生的一切`Damage`例如坠落伤害`UDamageType_Fall`、岩浆伤害`UDamageType_Lava`

当我们在类中指定伤害类型时，可以这么写：

```cpp
UPROPERTY(EditAnywhere, Category=Damage)
UClass* DamageType;
```

但这种方式会导致`DamageType`所能表示的类型不仅仅是`UDamageType`还能指向其他的`UClass`子类

如果使用`TSubclassOf`模板类：

```cpp
UPROPERTY(EditAnywhere, Category=Damage)
TSubclassOf<UDamageType> DamageType;
```

则会强制`DamageType`只能选择派生自`UDamageType`的类，并且在编辑器里也只会显示相关派生类

具体的，`TSubclassOf`还提供层级上的类型安全：

```cpp
UClass* ClassA = UDamageType::StaticClass();

TSubclassOf<UDamageType> ClassB;

ClassB = ClassA; // Performs a runtime check

TSubclassOf<UDamageType_Lava> ClassC;

ClassB = ClassC; // Performs a compile time check
```

上边代码中：
- ClassA 并不是`UDamageType`或其派生类，因此会进行**运行时检查**，确保 ClassA 所指向的对象的确是一个`UDamageType`或其派生类，如果不是则赋值失败
- ClassC 是`UDamageType`的一个派生类，该转换是合法的，会进行**编译时检查**
