# React Window 入门指南

## 简介

React Window 是一个帮助你快速渲染大型列表数据且无性能问题的组件库。它被广泛应用于许多地方，从 React DevTools 到 Replay 浏览器都在使用。

如果你需要渲染大型列表（数百或数千行），并且遇到以下性能问题：
- 初始渲染速度慢
- 滚动时卡顿或延迟
- 内存使用过高

那么 React Window 就是为你准备的解决方案。

## 安装

首先从 NPM 安装这个库：

```bash
npm install react-window
```

或者使用 yarn：

```bash
yarn add react-window
```

## 核心概念

React Window 通过"窗口化"（也称为"虚拟化"）技术来优化大型列表的渲染。这意味着在任意给定时间内，它只会渲染用户可见的列表项，加上一些额外的缓冲项以保证平滑滚动。

这种方法带来了显著的性能改善：
- 减少了需要渲染的 DOM 节点数量
- 降低了内存消耗
- 提升了初始渲染速度
- 实现了流畅的滚动体验

## 基础组件

React Window 提供了几种不同的组件来满足不同的使用场景：

### FixedSizeList
用于渲染具有固定高度的列表项。

```jsx
import { FixedSizeList as List } from 'react-window';

const Row = ({ index, style }) => (
  <div style={style}>
    Row {index}
  </div>
);

const Example = () => (
  <List
    height={150}        // 列表容器高度
    itemCount={1000}    // 列表项总数
    itemSize={35}       // 每个列表项的高度（像素）
    width={300}         // 列表容器宽度
  >
    {Row}
  </List>
);
```

### VariableSizeList
用于渲染高度可变的列表项。

```jsx
import { VariableSizeList as List } from 'react-window';

const Row = ({ index, style }) => (
  <div style={style}>
    Row {index}
  </div>
);

// 返回指定索引项的高度
const getItemSize = (index) => {
  return index % 2 === 0 ? 50 : 30;
};

const Example = () => (
  <List
    height={150}
    itemCount={1000}
    itemSize={getItemSize}
    width={300}
  >
    {Row}
  </List>
);
```

### FixedSizeGrid
用于渲染具有固定行列尺寸的二维网格。

```jsx
import { FixedSizeGrid as Grid } from 'react-window';

const Cell = ({ columnIndex, rowIndex, style }) => (
  <div style={style}>
    Item {rowIndex},{columnIndex}
  </div>
);

const Example = () => (
  <Grid
    columnCount={1000}  // 列数
    columnWidth={100}   // 每列宽度
    height={150}        // 网格容器高度
    rowCount={1000}     // 行数
    rowHeight={35}      // 每行高度
    width={300}         // 网格容器宽度
  >
    {Cell}
  </Grid>
);
```

### VariableSizeGrid
用于渲染行列尺寸可变的二维网格。

```jsx
import { VariableSizeGrid as Grid } from 'react-window';

const Cell = ({ columnIndex, rowIndex, style }) => (
  <div style={style}>
    Item {rowIndex},{columnIndex}
  </div>
);

// 返回指定列的宽度
const getColumnWidth = (index) => {
  return index % 2 === 0 ? 50 : 100;
};

// 返回指定行的高度
const getRowHeight = (index) => {
  return index % 2 === 0 ? 25 : 50;
};

const Example = () => (
  <Grid
    columnCount={1000}
    columnWidth={getColumnWidth}
    height={150}
    rowCount={1000}
    rowHeight={getRowHeight}
    width={300}
  >
    {Cell}
  </Grid>
);
```

## 重要注意事项

在使用 React Window 时，请特别注意以下几点：

### 必须使用 style 属性
列表项组件必须将 `style` 属性应用到根元素上：

```jsx
// 正确的做法
const Row = ({ index, style }) => (
  <div style={style}>
    Row {index}
  </div>
);

// 错误的做法 - 不要忽略 style 属性
const Row = ({ index }) => (
  <div>
    Row {index}
  </div>
);
```

这是因为 React Window 通过 [style] 属性传递定位信息（top, left等），如果不应用这些样式，列表项将无法正确定位。

### 列表项必须有明确的尺寸
对于 [FixedSizeList](file:///Users/liwudi/Desktop/tech-doc-translation/react-window/node_modules/.electron-npm-install-cache/_npx/506122dc12c0bb00/node_modules/@types/react-native/node_modules/@types/react/index.d.ts#L132-L137)，你需要提供 [itemSize](file:///Users/liwudi/Desktop/tech-doc-translation/electron-builder/node_modules/.electron-npm-install-cache/_npx/506122dc12c0bb00/node_modules/@types/react-native/node_modules/@types/react/index.d.ts#L132-L137) 属性；对于 [FixedSizeGrid](file:///Users/liwudi/Desktop/tech-doc-translation/react-window/node_modules/.electron-npm-install-cache/_npx/506122dc12c0bb00/node_modules/@types/react-native/node_modules/@types/react/index.d.ts#L132-L137)，你需要提供 [columnWidth](file:///Users/liwudi/Desktop/tech-doc-translation/electron-builder/node_modules/.electron-npm-install-cache/_npx/506122dc12c0bb00/node_modules/@types/react-native/node_modules/@types/react/index.d.ts#L132-L137) 和 [rowHeight](file:///Users/liwudi/Desktop/tech-doc-translation/electron-builder/node_modules/.electron-npm-install-cache/_npx/506122dc12c0bb00/node_modules/@types/react-native/node_modules/@types/react/index.d.ts#L132-L137) 属性。

## 支持项目

如果你喜欢这个项目，可以通过以下几种方式支持：

- 成为 GitHub 赞助者
- 成为 Open Collective 赞助者
- 请作者喝杯咖啡

## 总结

React Window 是 React Virtualized 的完全重写版本。作者没有试图解决尽可能多的问题或支持尽可能多的用例，而是专注于使包变得更小、更快。同时，作者在 API（和文档）设计上投入了很多思考，使其对初学者尽可能友好（尽管窗口化本身仍然是一个相对高级的用例）。