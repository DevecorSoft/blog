# React面向组件的设计原则与实践策略

## 背景和问题陈述

如果你正使用[React](https://reactjs.org/)来构建单页面应用程序，并且，我们通过创建和组合`react component`完成了相当多的功能，这正是一篇为你精心准备的文章。一般而言，前期我们确实能够快速的完成各个业务特性，然而，保持快速和稳定的价值交付通常面临巨大挑战。常常有这样一个特殊的阶段，一览当前项目的各个模块，不难识别到某些常见的问题，比如：存在“busy component”，边界不清的“helper”，依赖循环，“x.y.z"等等，它们零碎的分布在各个角落，如跗骨之蛆，如芒刺在背。那么此刻，我认为十分有必要探讨并澄清一种组件的组织策略，并以此作为我们现阶段的重构目标和设计原则。

### 基本假设

1. 名不正则言不顺: 我们应该给任何一个变量，函数，组件，api一个极具描述性的命名
2. 关注点分离
  * 将数据的使用和对数据的处理隔离开来
  * 在 React 的组件中，我们应该尽 可能少的在视图中添加计算逻辑
3. 得墨忒法则（The Law of Demeter）: 不与陌生人谈话，常见反模式：`x.y.z`
4. 无测试不重构
* React只是一个视图层的lib而不是一个框架这一个基本事实常常被忽略，MVC/MVP/MVVM中，React只是其中的V
* 隔离副作用能够让核心业务代码变得稳定和更具可测性
* YAGNI：You a'int gonna need it的首字母缩写，这个原则指出：你不需要增加某项能力直至真的被迫切需要
* SOLID：面向对象世界的五大设计原则
* CUPID：好代码的五个特质

### 面临的困境

#### 1. 复用和可维护性的冲突

```mermaid
flowchart LR
  Reusability(((可重用性)))
  bestBalancePoint([平衡点])
  maintainability(((可维护性)))

  Reusability --"<<<   组件变更的代价巨大   <<<"---
  bestBalancePoint --">>> 复用困难与代码复制   >>>"--- maintainability
```

> :memo: 通常，项目初期时，平衡点应该向可维护性倾斜，知道识别到大量的且稳定的重复逻辑，可以往可重用性倾斜

#### 2. 组件间的回调地狱

识别到了这样一个事实：我们将同一份数据以回调形式传递给父组件，并且历经5层回调。个人认为这是一个项目复杂性快速增长的信号，这个复杂性不在架构设计的缺陷，而在于业务本身的复杂性。我们大概需要一个状态管理工具，来管理复杂的组件间状态及隔离副作用。详见[02-state-management-library](02-state-management-library_ZH_CN.md)

## 提案

### 1. 组件分离关注点
  * 使用React的自定义hook来封装state和对state的处理（类似OOP的封装），让其内聚于一处。
  * `react component`只使用纯dom。

```typescript
export const TodoContainer = ({items}: Props) => {
    const {todos, addItem, markItemAsCompleted} = useTodos(items)

    return <div>
        <TodoInput onAddItem={addItem}/>
        <TodoList onClickItem={markItemAsCompleted} items={todos}/>
    </div>
}

interface UseTodos {
    readonly todos: TodoTask[]
    readonly addItem: (item: TodoTask) => void
    readonly markItemAsCompleted: (id: string) => void
}
```

### 2. 测试关注点分离

在遵循提案1的基础上，这个一个自然的显而易见的提议，单元测试，或者说组件测试应当有以下两种形式：
  * dom test：关注静态UI及其交互
  * state test: 关注于UI无关的业务逻辑，已经有成熟的测试工具，提供了这样的测试手段：[react hooks testing lib](https://react-hooks-testing-library.com/usage/basic-hooks)

> :memo: 在进行如此彻底的隔离之后，组件和view model的可测性大大增强，测试覆盖率将能够轻易接近100%

### 3. 边界约定
  * react component: 不直接维护状态的纯DOM组件，可以只是一个代码块，或者单文件组件
  * container (即当前项目的component): 一个包含下列文件的es module
    * 自定义hook文件，即view model (可选)
    * react component文件
    * index.ts
    * 用以消除依赖循环的type.ts接口定义文件（可选）
    * 其他配置文件 (可选)

> :memo: container间的关系应当是：父组件只依赖于直接子组件和global types，也就是说，不能import子组件及下的后代组件中的任何变量和类型

  * Page: 一个单react component文件，只包含静态DOM

### 4. 追求组合性
  * 使用Props注入来反转react component和自定义hook之间的依赖关系

```typescript
export const TodoContainer = ({items, todos, addItem, markItemAsCompleted}: Props) => {
    return <div>
        <TodoInput onAddItem={addItem}/>
        <TodoList onClickItem={markItemAsCompleted} items={todos}/>
    </div>
}
```
> :memo: 可以利用高阶组件在`index.ts`中注入state

## 优势分析

### [提案 1. 组件分离关注点]

* 良好, 因为 View和View model间彻底的隔离让组件看起来有序，一致，有边界，极大提升可读性
* 糟糕, 因为 失去了直接管理state的灵活性，业务变更将同时影响DOM和自定义Hook

### [提案 2. 测试关注点分离]

* 良好, 因为 rerender相关的测试用例看起来非常巨大，View和View model间彻底的隔离让测试免于处理rerender的用例
* 良好, 因为 View model能独立测试，边界条件更容易到达，避免了陷入在DOM中频繁的发起用户事件和编写大量桩代码的尴尬处境

### [提案 3. 边界约定]

* 良好，因为 通过划分层级带来的一致性能极大的提升可读性
* 良好，因为 container间的边界约定能够保持组件间的松散耦合，不至于牵一发而动全身
* 糟糕，因为 container间相互极力的隐藏实现，会造成一定的代码复制问题，并且通过提取共同点复用来减少代码复制并不容易
* 糟糕，因为 复杂性没有被平均到每一个层，container层拥有着最大得复杂性，除了container间依赖策略之外，没有明确的组织策略

### [提案 4.追求组合性]

* 良好，因为 更加彻底的隔离View与View model，极大的避免了二者间的依恋情结（同时被修改），在接口隔离的加持下，层间独立性和稳定性变强
* 糟糕，因为 不得不手动注入依赖，这导致`index.ts`变得复杂
* 糟糕，因为 `index.ts`不被测试覆盖，在没有e2e测试的情况下，故障率会显著上升

## 结论

* 选择提案1和提案2, 因为，相比较关注点分离所带来的可读性和可测性的提升，牺牲直接管理state带来的灵活性这件事显得微不足道。
* 选择提案3，因为，尽管占据最大复杂性的contioner层仍有陷入软件泥潭的倾向，但是相比于当前这样没有明确边界约定的组织方式，遵守提案3仍是一个巨大的提升

## Links

* [可维护的React](https://leanpub.com/maintainable-react-cn/c/4XeXfN9JDYd1) 免费获得渠道：邮件搜索 "一本关于React重构的小书"
* [CUPID](https://www.thoughtworks.com/radar/techniques?blipid=202203050)
* [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)
