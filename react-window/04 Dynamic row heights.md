# Dynamic row heights 动态元素高度

有时，一行的高度只有在渲染后才能确定。

以下是一份包含不同大小lorem ipsum文本的示例列表。通过点击“+”/“-”按钮，每行内容也可以切换为折叠/展开状态。

对于此类列表，react-window 提供了一个名为 useDynamicRowHeight 的辅助钩子。

```jsx
import { List, useDynamicRowHeight } from 'react-window'
import { RowComponent } from './ListRowDynamicRowHeights.example'

function Example({ lorem }: { lorem: string[] }) {
  const listState = useListState(lorem)
  const rowHeight = useDynamicRowHeight({
    defaultRowHeight: 50,
  })
  return (
    <List
      rowComponent={RowComponent}
      rowCount={lorem.length}
      rowHeight={rowHeight}
      rowProps={{ listState }}
    />
  )
}
```

在这种情况下，元素可以像往常一样呈现其内容，而react-window会为你测量其内容。

```jsx
import type { RowComponentProps } from 'react-window'

function RowComponent({
  index,
  listState,
  style,
}: RowComponentProps<{
  listState: ListState
}>) {
  const isCollapsed = listState.isRowCollapsed(index)
  const text = listState.getText(index)
  return (
    <div
      className={cn('p-2 cursor-pointer', {
        'bg-white/10': index % 2 === 0,
        'truncate': isCollapsed,
      })}
      onClick={() => listState.toggleRow(index)}
      style={style}
    >
      <ToggleIcon isCollapsed={isCollapsed} />
      {' '}
      {index}
      :
      {' '}
      {text}
    </div>
  )
}

```