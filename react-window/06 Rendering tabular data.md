# Rendering tabular data 渲染表格数据

使用列表组件可以呈现许多类型的表格数据。

上面的示例使用Flexbox布局来定位列和标题。

```jsx
import { getScrollbarSize, List, type RowComponentProps } from 'react-window'

function Example({ addresses }: { addresses: Address[] }) {
  const [size] = useState(getScrollbarSize)
  return (
    <div className="h-55 flex flex-col">
      <div className="flex flex-row bg-teal-600 p-1 px-2">
        <div className="grow flex flex-row items-center gap-2 font-bold">
          <div className="flex-1">City</div>
          <div className="flex-1">State</div>
          <div className="w-10">Zip</div>
        </div>
        <div className="shrink" style={{ width: size }} />
      </div>
      <div className="overflow-hidden">
        <List
          rowComponent={RowComponent}
          rowCount={addresses.length}
          rowHeight={25}
          rowProps={{ addresses }}
        />
      </div>
    </div>
  )
}

function RowComponent({
  index,
  addresses,
  style,
}: RowComponentProps<{
  addresses: Address[]
}>) {
  const address = addresses[index]
  return (
    <div className="flex flex-row items-center gap-2 px-2" style={style}>
      <div className="flex-1">{address.city}</div>
      <div className="flex-1">{address.state}</div>
      <div className="w-10 text-xs">{address.zip}</div>
    </div>
  )
}

```