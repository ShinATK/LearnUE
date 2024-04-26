
# 前置：状态机

[参考这里，回顾状态机的知识](../../状态机/README.md)

# 游戏里的状态机

## 前言

想象一下，在我们的游戏里有一个角色，他能跑能爬能跳，还能持有武器。如果我们在跳跃过程中按下爬伏按键，为了逻辑的正确，显然是需要在按下爬伏按键时判断一下目前角色是否处于跳跃状态 `if(!Player.IsJumping())`

但再想一下，如果我们正处于爬伏状态，此时按下了跳跃键，逻辑上说，我们同样不允许这种行为的发生，因而也需要在跳跃按键时判断目前角色是否处于爬伏状态 `if(!Player.IsCrouching())`

如果目前我们考虑的只是未持枪状态而持枪状态也需要这种一层一层的逻辑判断呢？

是否还要针对持枪爬伏、持枪跳跃，甚至是跳跃开火、爬伏开火等等进行设置判断条件，从而避免之前那种不符合逻辑的情况出现？

在《游戏设计模式：状态模式》中提到，这种思考方式的问题在于：它**不合法地捆绑了一堆布尔变量用于条件判断**，以及**将两种状态基类绑定到了同一个状态机上**

如果我们像之前那样进行耦合，假如角色在做什么（例如走路、跑步）有$N$个状态，角色携带什么（持枪和未持枪）有$M$个状态，耦合之后就是$M \cdot N$个状态。但如果分成两个状态机分别进行处理，最终只是$M+N$个状态。

## 有限状态机

回味之前我们想要实现的功能，一个角色，能够支持基本的移动（跑跳等），也能支持携带武器（装备和未装备）。

为了简单，这里暂时以**支持基本移动**为例。

按照之前的设想，我们希望角色的移动逻辑相互之间按照某种规则进行转换，比如：
- 静止状态时，按下 w 变为走路状态
- 走路状态时，按下 shift 变为跑步状态
- 跑步状态时，松开 shift 变为走路状态
- 走路或跑步状态时，松开 w 变为静止状态

可以发现，上边的情景描述很符合之前讨论过的有限状态机
- 有限状态：
  - 静止状态
  - 走路状态
  - 跑步状态
- 转移：
  - 静止到走路
  - 走路到跑步
  - 等等
- 转移条件：
  - 按下/松开 shift
  - 按下/松开 w
- 动作：
  - 状态中：静止状态循环播放静止时的角色动画，其他状态同理
  - 状态进入和离开时：播放动画之间的衔接动画

## 利用枚举和分支实现简单的有限状态机

按照之前的分类，我们可以使用枚举值来代表角色可能的这三种运动状态：

```cpp
enum MovementState
{
  IDLE,
  WALK,
  RUN
};
```

然后给角色中设置一个枚举值用来记录当前角色所处的状态：

```cpp
class Player
{
  // ...
public:
  MovementState State;
};
```

于是，我们可以利用`switch`语句来很方便的实现不同状态下，角色的行为逻辑：

```cpp
void Player::HandleInput(Input input)
{
  switch(State)
  {
    case IDLE:
      if(input == key_w_pressed)
      {
        State = WALK;
        PlayAnim(Anim_Walk);
      }
    case WALK:
      if(input == key_shift_pressed)
      {
        State = RUN;
        PlayAnim(Anim_RUN);
      }
      else if(input == key_w_released)
      {
        State = IDLE;
        PlayAnim(Anim_Idle);
      }
    case RUN:
      if(input == key_shift_released)
      {
        State = WALK;
        PlayAnim(Anim_Walk);
      }
      else if(input == key_w_released)
      {
        State = IDLE;
        PlayAnim(Anim_Idle);
      }
    default:
      break;
  }
}
```

这样，如果我们想要添加新的状态，设置起来就很方便了，比如增加一个跳跃状态

```cpp
case OTHER_STATE:
  if(/* ... */) { /* ... */ }
  else if(/* ... */) { /* ... */ }
  else if(input == key_space_pressed)
  {
    State = JUMP;
    PlayAnim(Anim_Jump);
  }
case JUMP:
  if(CheckPlayerIsGround())
  {
    State = IDLE;
    PlayAnim(Anim_Idle);
  }
```

可以看到，通过枚举值和分支，我们在不同的角色状态下实现了不同的输入处理逻辑，从而避免了布尔变量的无限增殖。

不过，其实还有问题。

角色每多一个状态，就需要对应的增加一个枚举值与增加一个`case`分支单独设置逻辑，如果角色的状态很多的话，实际上这种方式并没有从根本上解决臃肿且冗长代码的问题，其实仔细分析就可以发现，这里边其实有很多逻辑都是共同的。

比如，只要松开 w，无论是走路还是跑步，都会变为静止状态；只要按下 space，无论是静止、走路还是跑步，都会变为跳跃状态。

但是像我们这样实现之后，每个分支里都要单独实现一次，虽然我们可以把共同的部分封装为函数/方法，但结果上仍然是需要每个分支里都单独调用一次。

我想说的是，**这还不够抽象**，我们需要进一步分析，找出更深层次的共性。

> 不过值得肯定的时，如果角色状态很少，比如上边就三个：静止、走路、跑步，直接有限状态机即可
> 
> ~~要用简单的方法打败简单的问题 by 老爹~~
>
> 书中在这里还提到了[更新方法](https://gpp.tkchu.me/update-method.html)，这里暂时专注状态模式


## 状态模式

GOF 中对状态模式的描述为：

> 允许一个对象在其内部发生变化时改变自己的行为，该对象看起来像是改变了它的行为

不得不承认语言的魅力，这句话虽然是对状态模式的描述，但实际上，先前我们利用枚举实现的有限状态机也符合这句话的描述。

这种模棱两可的，似乎准确描述了这个名词所代表的深层含义，却又有着看似泛泛的停留于表面描述，“你改变了自己的行为，看起来就像改变了自己行为似的”，导致最终读起来就像是：

~~“你说了一句话跟说了一句话似的”~~

好在，《游戏设计模式》的作者详细给出了这句话背后的含义（至少对于目前来说是这样）：
- **状态基类**
- **状态接口**
- **状态委托**

根据之前我们的分析，这些状态其实都有着共同的逻辑接口，比如接收并处理输入的 `HandleInput()`，如果我们创建一个**状态基类**，并**提供相应的接口（虚函数）表示状态的行为**，其他状态都继承自该基类，这样每个状态都可以针对性实现自己的行为逻辑（`override`）。同时，由于这些具体的状态都继承自同一个状态基类，所以我们可以在角色中维护一个**状态基类的指针**，从而使得指针指向不同状态时，角色调用同样签名的接口所表现出来的行为逻辑是和当前角色状态相关的。

举个例子说明：

在玩家类中增加**状态基类指针**，以及玩家状态方法：

```cpp
class BaseState
{
public:
    virtual ~BaseState() = 0
    virtual void HandleInput(Player& player, Input input) = 0
    virtual void Update(Input input) = 0
};
class Player
{
public:
    virtual void HandleInput(Input input)
    {
        State->HandleInput(*this, input);
    }
    virtual void Update(Input input)
    {
        State->Update(*this);
    }
public:
  BaseState* State;
};
```

如果我们创建的状态子类如下：

```cpp
class WalkState : public BaseState
{
public:
    WalkState() {}
    ~WalkState() {}
public:
    virtual void HandleInput(Player& player, Input input)
    {
        if(input == key_shift_pressed)
        {
            player.PlayAnim(Anim_Run);
            // 这里要将 player 状态切换为 RunState
        }
        else if(input == key_w_released)
        {
            player.PlayAnim(Anim_Idle);
            // 这里要将 player 状态切换为 IdleState
        }
    }
    virtual void Update(Input input);
};
```

当我们想要改变玩家状态时，只需要修改`State`指针，指向不同的`BaseState`对象，从而使得调用的`HandleInput()`方法的行为发生了变化，看起来就像是角色的状态发生了改变。

到这里，再回去理解本节开篇那句话，就好理解一些了。

> 书中还提到了该设计模式与[策略模式](https://en.wikipedia.org/wiki/Strategy_pattern)和[类型对象模式](https://gpp.tkchu.me/type-object.html)的相似之处，区别在于这些模式的**意图**
>
> 策略模式的目标：**解耦**主类和它的部分行为
> 类型对象的目标：**共享**对相同类型对象的引用，让**一系列对象行为相近**
> 状态模式的目标：让主对象通过**改变委托对象**，从而**改变自身行为**


## 对于“状态对象”的讨论

在之前的状态模式中，我们实现了：
- 状态：同一个状态基类派生的不同的状态子类
- 状态的转换条件：`if(input == key_w_pressed)`等

对于状态的动作，这里其实只实现了一部分，详细讨论放在后边。这里先提一下对于“**状态转移**”的讨论。

在之前的代码中，其实还没有实现状态的切换。

```cpp
void WalkState::HandleInput(Player& player, Input input)
{
    if(input == key_shift_pressed)
    {
        player.PlayAnim(Anim_Run);
        // 这里要将 player 状态切换为 RunState
    }
    // ...
}
```

思考一下如何切换这个状态呢？或者说，这个新状态从哪里来的？如果是`enum`实现的，就和一个基本类型的变量一样，重新设置就行了。但现在这些状态都是一个个类，意味着**需要指向一个类的实例**

### 方案一：静态状态

按照目前实现的状态来看，这个状态类中只存在一些虚函数，那么它内部需要存储的唯一数据其实就是一个**指向虚表的指针**。那么就没有必要产生多个实例了，反正要它的实例也仅仅是为了拿虚表中的方法。

> 也可以直接将这些方法修改为普通的顶层函数，而不是封装到类中
> 这样，只需要把主类中的`State`改为一个函数指针

所以，我们可以创建一个**静态实例**，具体放在哪里看自己选择了。

> [享元模式](https://gpp.tkchu.me/flyweight.html)

不过，这种静态状态会导致的问题是，如果有多个玩家或者说双人玩家合作，如果共享同一个静态状态，就会导致角色状态之间的干扰。

### 方案二：实例化状态

最直接的解决方法就是进行一个状态类的实例化。

不过这种情况要考虑的问题是：如何**正确的释放当前的状态**

我们先来修改一下状态子类中的`HandleInput()`方法：

```cpp
StateBase* WalkState::HandleInput(Player& player, Input input)
{
    if(input == key_shift_pressed)
    {
        player.PlayAnim(Anim_Run);
        return new RunState(); // 返回新状态
    }
    return nullptr; // 保持原样
}
```

修改后的版本中添加了新状态的创建，如果返回一个空指针，表示我们保持玩家当前状态不变。

接下来是玩家的`HandleInput()`实现：

```cpp
void Player::HandleInput(Input input)
{
    StateBase* new_state = State->HandleInput(*this, input);
    if(new_state != nullptr)    // 如果返回了一个新状态
    {
        delete State;           // 释放旧状态
        State = new_state;      // 更新为新状态
    }
}
```

### 两种方案的对比

实例化的方案似乎更符合简单的直觉，玩家的状态改变，就是旧状态的销毁和新状态的创建。但如果玩家的状态切换十分频繁，比如只狼中狼的状态，如果我们给狼的状态设置一个格挡态，那么按照只狼基于弹反和格挡的游戏节奏，势必会出现频繁的弹反态/格挡态到普通状态之间的切换（想象一下拼刀时候的节奏）

如此频繁的销毁和创建，势必会对性能上产生影响。

作为一个程序员，那必然是以“**用最少的动作，做最多的事情**”为准则的，无脑地在 new 和 delete 之间来回横跳，不如直接使用**方案一：静态状态**。

不过，既然频繁创建和销毁会影响性能，那我直接提前创建好不就行了，切换的时候把手里的放下，拿个新的就好了。对于这种实现，可以采用[**对象池模式**](https://gpp.tkchu.me/object-pool.html)


## 定义入口和出口行为

按照我们的想法，状态模式的目的就是**将状态的行为和数据封装到同一类中**。对于我们的状态类，现在还有一点问题没有实现。

回想一下，我们在进行状态切换的时候，比如从`WalkState->IdleState`，设置`IdleState`状态的动画，却是在`WalkState`中设置的，不是`WalkState`的行为被放在了其中。

```cpp
StateBase* WalkState::HandleInput(Player& player, Input input)
{
    if(input == key_w_released)
    {
        player.PlayAnim(Anim_Idle); // 新状态相关的东西，却在不相关的旧状态里进行了设置
        return new IdleState();
    }
    return nullptr;
}
```

如何解决这个问题呢？

试想一下，当我们处于某个状态时，这个状态会表现出其自己的行为和动作。当某个时刻满足了另一个状态的转移条件，我们应当停止当前状态，并切换到新的状态。

如果我们想要将设置新状态的相关操作从旧状态中解耦出来，势必要增加一个**进入方法**，也即，进入某个状态时，需要做的工作，类似初始化。

这个就是**入口行为 EntryAction**

所以代码可以修改为：

```cpp
/* WalkStae 中 */
StateBase* WalkState::HandleInput(Player& player, Input input)
{
    if(input == key_w_released)
    {
        return new IdleState();
    }
    return nullptr;
}

/* IdleState 中 */
void IdleState::EntryAction(Player& player)
{
  player.PlayerAnim(Anim_Idle);
  // 其他动作
}

/* Player 中 */
void Player::HandleInput(Input input)
{
  BaseState* new_state = State->HandleInput(*this, input);
  if(new_state != nullptr)
  {
    State->Exit(); // 旧状态的出口行为
    delete State;

    State = new_state;
    new_state->enter(*this); // 新状态的入口行为
  }
}
```

这样，通过定义**入口行为和出口行为**，我们实现了状态之间的动作解耦，状态不在需要关心自己要再进入的是什么状态，它只需要关心：
- 当从其他状态进入自己这个状态之前时，要做什么：主要是初始化的设置
- 当玩家处于自己这个状态时，会做什么：主要行为逻辑和转移条件判断
- 当离开自己这个状态进入其他状态之前时，发生什么：可能是两个状态之间的衔接动画，也可能是取消自己状态时的一些设置等等



## 完善状态模式

就像我们之前提到的，**我们将两种状态绑定到了一个状态机上——做的和携带的**。为了处理所有状态，比如角色现在可以持枪，这导致我们的状态又增加了在持枪状态下的 `class IdleStateWithGun;`，`class WalkStateWithGun;`，`class RunStateWithGun;`，似乎我们之前实现的状态模式并没有很好的解决这个问题。

### 并发状态机

可以通过设置两个状态机解决这个问题

```cpp
class Player
{
public:
  BaseState* State;
  BaseState* Equipment;
};
```


### 分层状态机

如果按照先前的方式会发现，在同表示静止的`IdleState`和`IdleStateWithGun`中存在相同的逻辑代码

于是思考，是否可以按照`BaseState`和其余子类派生关系，将`IdleState`和`IdleStateWithGun`又进行封装抽象？

实现一个`GroundState`，用来表示所有地面上的状态，其余的`IdleState`等继承自该状态，子类可以利用重载来修改父类的方法。

```cpp
class IdleState : public OnGroundState
{
public:
  virtual void HandleInput(Player& player, Input input)
  {
    if (input == key_w_pressed)
    {
      // 进入走路状态
    }
    else
    {
      // 没有处理输入，返回上一层
      OnGroundState::HandleInput(heroine, input);
    }
  }
};
```

另一种实现分层的方法是利用**状态栈**，而不是用单一状态来表示当前状态及其父类状态

> 在状态栈中，
>
> 栈顶状态为角色当前状态，在其之下为直接父类状态，依次类推。
>
> 如果需要一个状态的特定行为，可以从栈顶开始寻找直到找到

### 下推自动机

下推自动机也利用到了状态栈，但这里的栈和上边提到的状态栈表示不同的意义。

设想一个场景，角色根据输入进入了某个状态，但这个状态是有时限的，一旦时间结束，就需要重新回到之前的旧状态。

你可能会说，那我直接在这个状态里设置一个转移不就好了？

但回想我们之前新旧状态转移过程，我们只是“销毁”旧状态，“创建”新状态，从来没有记录过当前状态是从哪个状态变过来的。

重复一下这里想要解决的问题是：当角色需要恢复到之前的旧状态时，它能自己回想起来并进行切换，但有限状态机又不会记录历史。

这里同样可以考虑栈这种数据结构。当进入某个状态时，状态入栈；当满足转移条件，如果状态之间相互独立则旧状态出栈，新状态入栈；如果状态之间像是我们之前描述的那种，需要记忆之前旧状态，则直接入栈，等新状态结束出栈，角色就可以自动回到原来的旧状态了。

应用举例：角色的开火状态，角色可以从静止、走路、跑步状态进入开火状态，开火完毕后又可以回到原来的状态。
- 角色开火：开火状态入栈
- 角色停止开火：开火状态出栈，角色进入新的栈顶状态



## 暂时的总结

- 对于状态少，行为简单的情况，直接使用枚举值和分支实现有限状态机最为方便
- 当状态变多，且行为逻辑稍微复杂时，适合利用**状态模式**对状态和相关行为进行封装。这其中，有两种实现具体**状态对象**的方式：
  - 静态状态
    - 不需要频繁创建和销毁，对性能友好
    - 遇到本地多人支持时，需要想办法解决多个角色公用一套状态对象的解耦问题
  - 实例化状态
    - 对多人游戏友好
    - 需要频繁创建和销毁，对性能不友好，但可以通过**对象池模式**来避免频繁的创销
- 定义入口行为和出口行为，实现状态之间的解耦，每个状态只关心自己的行为，而不需要考虑其他状态的行为
- 多种状态之间的依赖关系可以利用：并发状态机，设置多个状态机；分层状态机，利用封装和继承；下推自动机，利用栈。


---
# 参考链接
- [游戏设计模式中的状态模式](https://gpp.tkchu.me/state.html)
  - [维基百科：状态模式](https://zh.wikipedia.org/wiki/%E7%8B%80%E6%85%8B%E6%A8%A1%E5%BC%8F)
  - [本地笔记](../《游戏设计模式》/README.md)