# Fixed row heights 固定元素高度

最简单的列表类型是具有固定行高的列表。

要呈现这种列表，你需要指定它包含多少行（rowCount）、哪个组件应该呈现行（rowComponent）以及每行的高度（rowHeight）：

```jsx
import { List } from "react-window";
function Example({ names }: { names: string[] }) {
  return (
    <List
      rowComponent={RowComponent}
      rowCount={names.length}
      rowHeight={25}
      rowProps={{ names }}
    />
  );
}
```
rowProps对象还可以用于组件间的共享。在rowProps中传递的值也会作为属性传递给行组件：
```jsx
import { type RowComponentProps } from "react-window";

function RowComponent({ index, names, style }: RowComponentProps<{ names: string[] }>) {
  return (
    <div className="flex items-center justify-between" style={style}>
      {names[index]}
      <div className="text-slate-500 text-xs">{`${index + 1} of ${names.length}`}</div>
    </div>
  );
}
```
