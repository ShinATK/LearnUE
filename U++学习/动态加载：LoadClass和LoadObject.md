
`LoadClass<T>()` 和 `LoadObject<T>()` 用来加载资源

注意这里的模板参数需要写成对应蓝图创建时继承的类，例如父类为`Actor`，则要写成`LoadClass<AActor>()`

一个示例：

```cpp
UClass* Test = LoadClass<AActor>(NULL, TEXT("Blueprint'/Game/Blueprints/MapPathBrush_BP.MapPathBrush_BP_C'"));  
```

参考资料：
- https://blog.csdn.net/a359877454/article/details/80523354
