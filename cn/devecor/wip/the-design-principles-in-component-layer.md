# [ADR-01] [The component-oriented principles]

* Status: [accepted | deprecated | superseded by [ADR-005](./005-example.md)]
* Date: 2022-10-26 (when the decision was last updated)

## Context and Problem Statement

We are using [React](https://reactjs.org/) to build an single page application and accomplished great jobs by creating and compose `react component`. Definitely we are moving fast, However, to keep moving fast as we are is of more value. There are some `tech debt` recognized in our web system, such as "busy component", so many "helper", dependency loop, "x.y.z", etc. it's necessary to clarify how to organize components and how to test them.

### Assumptions
* 名不正则言不顺: you should have a proper naming as it is
* Separation of concern
  * 将数据的使用和对数据的处理隔离开来，在 React 的组件中，我们应该尽 可能少的在视图中添加计算逻辑。
* The Law of Demeter: do not talk about strangers
* 无测试不重构
* React只是一个视图层的lib而不是一个框架这一个基本事实常常被忽略，MVC/MVP/MVVM中，React只是其中的V
* Isolate effects can be benefit
* YAGNI
* SOLID
* CUPID

### Constraints
* Reusability and maintainability are incompatible in component

```mermaid
flowchart LR
  Reusability(((可重用性)))
  bestBalancePoint([平衡点])
  maintainability(((可维护性)))

  Reusability --"<<<   组件变更的代价巨大   <<<"---
  bestBalancePoint --">>> 复用困难与代码复制   >>>"--- maintainability
```

> :memo: 通常，项目初期时，平衡点应该向可维护性倾斜，知道识别到大量的且稳定的重复逻辑，可以往可重用性倾斜

## Proposal

1. Separation of concern
  * customize hook to encapsulate data/state and its procedures (*)
  * pure dom in react component (*)

```typescript
export const TodoContainer = ({items}: Props) => {
    const {todos, addItem, markItemAsCompleted} = useTodos(items)

    return <div>
        <TodoInput onAddItem={addItem}/>
        <TodoList onClickItem={markItemAsCompleted} items={todos}/>
    </div>
}

interface useTodos {
    readonly todos: TodoTask[]
    readonly addItem: (item: TodoTask) => void
    readonly markItemAsCompleted: (id: string) => void
}
```

2. Tests
  * dom test (static ui and its behavior)
  * state test (states and its api): [react hooks testing lib](https://react-hooks-testing-library.com/usage/basic-hooks)
  * coverage 100%

3. Boundaries
  * react component: pure static dom without state, can be a code block or a single file inside a module

  * element module (component): a js module that contains following files
    * pure custom hook files (optional)
    * react component files
    * index.ts
    * other files (optional)
  父组件只依赖于直接子组件和global types，也就是说，不能import子组件及下的后代组件中的任何变量和类型

  * Page: pure static dom and a single file
    * single react component file

4. Composition
  * Inject states that maintained by customized hook via props (看起来像在创造一个状态管理的轮子)

```typescript
export const TodoContainer = ({items, todos, addItem, markItemAsCompleted}: Props) => {
    return <div>
        <TodoInput onAddItem={addItem}/>
        <TodoList onClickItem={markItemAsCompleted} items={todos}/>
    </div>
}
```
> :memo: 可以利用高阶组件在`index.ts`中注入state

  * Isolate api call with high-order component: `withApiCall`
```typescript
export const withEffect = <CompProps extends Effects, Effects>(Component: ComponentType<CompProps & typeof effect>, effect: Effects) =>
  (props: Omit<CompProps, keyof Effects>): React.ReactElement =>
    <Component {...{...effect, ...props} as CompProps & JSX.IntrinsicAttributes} />
```

## Decision Outcome

Chosen option: "[option 1]", because [justification. e.g., only option, which meets k.o. criterion decision driver | which resolves force force | … | comes out best (see below)].


## Pros and Cons of Proposal <!-- optional  -->

### [option 1]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

### [option 2]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

### [option 3]

[example | description | pointer to more information | …] <!-- optional -->

* Good, because [argument a]
* Good, because [argument b]
* Bad, because [argument c]
* … <!-- numbers of pros and cons can vary -->

## Links <!-- optional -->

* [可维护的React](https://leanpub.com/maintainable-react-cn/c/4XeXfN9JDYd1)： 邮件搜索 "一本关于React重构的小书"
* … <!-- numbers of links can vary -->
