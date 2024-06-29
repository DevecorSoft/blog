# 基于FSM的React组件设计

## 什么是FSM

## 1. 单组件FSM

![image](https://devecor.cn/image/8bdc3c16-422d-4c43-8398-9d29fe9f1047/image.png)

1. 似乎暗含了存在六个状态？
2. 看起来需要封装3个组件： row dropdown modal
3. 看起来有复杂的交互

![image](https://devecor.cn/image/1a3dbc0a-3687-4b2f-8ad3-14a8327769a9/image.png)

一个组件，3个状态：`RowState: 'row' | 'dropdownOpen' | 'dialogOpen'`

## 2. state数量越少越好

```
RowState: 'row' | 'dropdownOpen' | 'dialogOpen'
```

```
isDialogOpen: boolean
isDropdownOpen: boolean
isRowHighlight: boolean
```

## 3. state需要被封装在合适的位置

```mermaid
flowchart
  root --> table --> styledRow --> row --> cell --> dropdown
  cell --> modal
  table --> newRow
  style newRow fill:#bbf,stroke:#f66,stroke-width:2px,color:#fff,stroke-dasharray: 5 5
```

```typescript
const [cellState, setCellState] = useState('row')

setCellState('dropdown')
setRowStyle('highlight')
```
