# Rendering a grid 渲染网格

使用网格组件渲染具有许多行和列的数据：

网格要求您指定行数和列数以及每行的宽度和高度：

```jsx
import { Grid } from 'react-window'

function Example({ contacts }: { contacts: Contact[] }) {
  return (
    <Grid
      cellComponent={CellComponent}
      cellProps={{ contacts }}
      columnCount={10}
      columnWidth={columnWidth}
      rowCount={contacts.length}
      rowHeight={25}
    />
  )
}
```
列宽和行高可以是数字或函数。在上面的示例中，行高是固定的，列宽是根据列索引确定列宽度的函数：

```jsx
function columnWidth(index: number) {
  switch (indexToColumn(index)) {
    case 'address': {
      return 250
    }

    case 'email': {
      return 300
    }

    case 'job_title': {
      return 150
    }

    case 'timezone': {
      return 200
    }

    case 'zip': {
      return 75
    }

    default: {
      return 100
    }
  }
}
```
最后，给定列和行索引，网格需要一个组件来渲染单元格。与列表一样，此组件接收额外的属性作为 cellProps 一部分

```jsx
import type { CellComponentProps } from 'react-window'

function CellComponent({
  contacts,
  columnIndex,
  rowIndex,
  style,
}: CellComponentProps<{
  contacts: Contact[]
}>) {
  const address = contacts[rowIndex]
  const content = address[indexToColumn(columnIndex)]
  return (
    <div className="truncate" style={style}>
      {content}
    </div>
  )
}
```