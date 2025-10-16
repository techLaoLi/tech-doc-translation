# react-virtualized-auto-sizer

> 来自 react-virtualized 的 AutoSizer 组件的独立版本

```bash
# Yarn
yarn add react-virtualized-auto-sizer

# NPM
npm install --save react-virtualized-auto-sizer
```

## 简介

react-virtualized-auto-sizer 是一个高阶组件（HOC），它能够自动调整尺寸以适应所有可用空间，并将宽度和高度值传递给其子组件。这个组件通常与 react-window 或 react-virtualized 一起使用，用于创建能够响应父容器尺寸变化的虚拟化列表。

## 属性 (Props)

| 属性 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| `children` | `({ height: number, width: number }) => React.Element` |  | 渲染属性（Render prop），接收宽度和高度参数 |
| `className` | `string` | `undefined` | 传递给 AutoSizer 组件根元素的 CSS 类名 |
| `defaultHeight` | `number` | `undefined` | 服务器端渲染时使用的默认高度 |
| `defaultWidth` | `number` | `undefined` | 服务器端渲染时使用的默认宽度 |
| `disableHeight` | `boolean` | `false` | 禁用高度调整功能 |
| `disableWidth` | `boolean` | `false` | 禁用宽度调整功能 |
| `nonce` | `string` | `undefined` | 内容安全策略（nonce） |
| `onResize` | `({ height: number, width: number }) => void` | `undefined` | 尺寸变化时的回调函数 |
| `style` | `Object` | `undefined` | 传递给 AutoSizer 组件根元素的样式对象 |

## 基本使用

```jsx
import React from 'react';
import AutoSizer from 'react-virtualized-auto-sizer';

const MyComponent = () => (
  <AutoSizer>
    {({ height, width }) => (
      <div style={{ height, width }}>
        {/* 你的内容 */}
      </div>
    )}
  </AutoSizer>
);
```

## 与 react-window 结合使用

AutoSizer 组件经常与 react-window 一起使用，以便创建能够自动适应容器尺寸的虚拟化列表：

```jsx
import React from 'react';
import AutoSizer from 'react-virtualized-auto-sizer';
import { FixedSizeList as List } from 'react-window';

const MyList = ({ items }) => (
  <AutoSizer>
    {({ height, width }) => (
      <List
        height={height}
        itemCount={items.length}
        itemSize={35}
        width={width}>
        {({ index, style }) => (
          <div style={style}>
            {items[index]}
          </div>
        )}
      </List>
    )}
  </AutoSizer>
);
```

## 服务器端渲染

在服务器端渲染（SSR）时，由于无法获取 DOM 元素的实际尺寸，可以使用 `defaultHeight` 和 `defaultWidth` 属性提供初始尺寸：

```jsx
import React from 'react';
import AutoSizer from 'react-virtualized-auto-sizer';

const MyComponent = () => (
  <AutoSizer
    defaultHeight={600}
    defaultWidth={800}>
    {({ height, width }) => (
      <div style={{ height, width }}>
        {/* 你的内容 */}
      </div>
    )}
  </AutoSizer>
);
```

## 核心功能

1. **自动尺寸调整**：AutoSizer 组件会自动计算并传递其父容器的宽度和高度给子组件，使得子组件可以根据父容器的大小变化而变化。

2. **服务器端渲染友好**：通过提供默认的宽度和高度值，可以在服务器端渲染时预先设置尺寸，避免渲染过程中的抖动。

3. **高度/宽度固定**：如果需要，可以使用 `disableHeight` 或 `disableWidth` 属性固定子组件的高度或宽度，使其不会随父容器的大小变化而变化。

4. **内容安全策略支持**：通过提供 nonce 属性，可以与内容安全策略（CSP）配合使用，增加应用的安全性。

## 应用场景

react-virtualized-auto-sizer 特别适用于以下场景：

1. 创建响应式虚拟化列表或表格
2. 需要根据父容器尺寸动态调整组件大小的场景
3. 服务器端渲染的应用程序
4. 需要处理大量数据展示的 Web 应用
5. 与 react-window 或 react-virtualized 结合使用的项目