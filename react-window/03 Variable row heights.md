# Variable row heights 可变元素高度

包含不同类型行的列表可能需要不同的高度来呈现。

以下是一个按州分组的人口最多的美国邮政编码示例。州行的“标题”更高，样式也不同。

这个列表需要一个rowHeight函数，该函数根据列表中包含的数据类型告诉它每行应该有多高。

```jsx
import {
  List,
  type ListImperativeAPI,
  type RowComponentProps,
} from 'react-window'

type Item =
  | { type: 'state', state: string }
  | { type: 'zip', city: string, zip: string }
interface RowProps {
  items: Item[]
}

function Example({ items }: { items: Item[] }) {
  return (
    <List<RowProps>
      rowComponent={RowComponent}
      rowCount={items.length}
      rowHeight={rowHeight}
      rowProps={{ items }}
    />
  )
}

function rowHeight(index: number, { items }: RowProps) {
  switch (items[index].type) {
    case 'state': {
      return 30
    }

    case 'zip': {
      return 25
    }
  }
}

function RowComponent({ index, items, style }: RowComponentProps<RowProps>) {
  const item = items[index]
  const className = getClassName(item)
  return (
    <div className={className} style={style}>
      {item.type === 'state'
        ? (
            <span>{item.state}</span>
          )
        : (
            <span>
              {item.city}
              ,
              {item.zip}
            </span>
          )}
      <div className="text-slate-500 text-xs">{`${index + 1} of ${items.length}`}</div>
    </div>
  )
}

```


