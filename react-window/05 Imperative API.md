# Imperative API 必要的API

List为响应事件提供了一个必需的API。访问此API的推荐方法是使用导出的ref钩子：

```jsx
import { useListRef } from "react-window";
```

Attach the ref during render:

```jsx
function Example(props: Props) {
  const listRef = useListRef(null)
  return <List listRef={listRef} {...props} />
}
```

并在事件处理程序中调用API方法：

```jsx
function onClick() {
  const list = listRef.current
  list?.scrollToRow({
    align: 'auto', // optional
    behavior: 'auto', // optional
    index: 250,
  })
}
```

注意：如果要将ref传递给另一个组件或钩子，请改用ref回调函数。

```jsx
import { useListCallbackRef } from 'react-window'

function Example(props: Props) {
  const [list, setList] = useListCallbackRef(null)
  useCustomHook(list)
  return <List listRef={setList} {...props} />
}
```