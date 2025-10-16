# react-window-infinite-loader

> 受 react-virtualized 启发但专为 react-window 设计的 InfiniteLoader 组件

```bash
# Yarn
yarn add react-window-infinite-loader

# NPM
npm install --save react-window-infinite-loader
```

## 属性(Props)

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `children` | `({ onItemsRendered: Function, ref: React$Ref }) => React$Node` | 渲染属性(Render prop)。请参见下面的示例用法。 |
| `isItemLoaded` | `(index: number) => boolean` | 负责跟踪每个项目加载状态的函数。 |
| `itemCount` | `number` | 列表中的行数；如果实际数量未知，可以是任意大的数字。 |
| `loadMoreItems` | `(startIndex: number, stopIndex: number) => Promise<void>` | 当需要加载更多行时调用的回调函数。它应该返回一个在所有数据加载完成时解决的 Promise。 |
| `minimumBatchSize` | `?number` | 每次加载的最小行数；默认为 10。此属性可用于批处理请求以减少 HTTP 请求。 |
| `threshold` | `?number` | 预获取数据的阈值；默认为 15。阈值为 15 意味着当用户滚动到距离底部 15 行以内时开始加载数据。 |

## 创建无限加载列表

以下代码片段展示了如何使用 `InfiniteLoader` 组件来包装 `react-window` 中的 `FixedSizeList` 或 `VariableSizeList` 组件。

```jsx
// 这个值是任意的。
// 如果你知道远程数据的大小，可以提供一个真实的值。
// 你也可以像下面示例中那样逐渐增加这个值。
const itemCount = 1000;

<InfiniteLoader
  isItemLoaded={isItemLoaded}
  itemCount={itemCount}
  loadMoreItems={loadMoreItems}
>
  {({ onItemsRendered, ref }) => (
    <FixedSizeList
      itemCount={itemCount}
      onItemsRendered={onItemsRendered}
      ref={ref}
      {...otherListProps}
    />
  )}
</InfiniteLoader>
```

## 在线示例

你可以在 Code Sandbox 上试用这个组件。

## 项目介绍

react-window-infinite-loader 是一个基于 react-window 的无限加载组件。它借鉴了 react-virtualized 的设计理念，但更加专注于与 react-window 的集成。通过这个组件，您可以轻松地将无限加载功能添加到您的列表中，无论是固定大小的列表还是可变大小的列表。

### 核心功能

- **无限加载**：react-window-infinite-loader 的核心功能是实现无限加载。它通过监听用户的滚动行为，动态加载数据，确保用户在滚动时不会遇到卡顿或延迟。
- **懒加载**：组件支持懒加载，只有在用户滚动到接近列表底部时才会触发数据加载，从而减少不必要的网络请求。
- **缓存管理**：组件内置了缓存机制，可以有效地管理已加载的数据，避免重复加载。

## 技术栈

- **React**：项目基于 React 框架，充分利用了 React 的组件化开发模式。
- **react-window**：作为核心依赖库，react-window 提供了高效的虚拟化列表渲染功能，确保在大数据量下依然能够保持高性能。

## 基本使用

以下是一个简单示例，展示了如何使用 react-window-infinite-loader 渲染一个具有无限加载功能的列表：

```jsx
import { FixedSizeList as List } from 'react-window';
import InfiniteLoader from 'react-window-infinite-loader';

// 在你的组件中
const itemCount = 1000; // 可以是任意大的数字

<InfiniteLoader
  isItemLoaded={isItemLoaded}
  itemCount={itemCount}
  loadMoreItems={loadMoreItems}
>
  {({ onItemsRendered, ref }) => (
    <List
      itemCount={itemCount}
      onItemsRendered={onItemsRendered}
      ref={ref}
      // 其他属性...
    >
      {Row}
    </List>
  )}
</InfiniteLoader>
```

## 应用场景

react-window-infinite-loader 特别适用于需要展示大量数据的场景，如：

1. 社交媒体信息流（如 Facebook 或 Twitter）
2. 电商产品列表
3. 消息记录列表
4. 大型数据表格
5. 任何需要高性能滚动加载的列表场景