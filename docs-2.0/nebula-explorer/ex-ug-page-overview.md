# 页面概览

本文介绍 Explorer 的基本组成，帮忙用户了解 Explorer 的各功能。

Explorer 由导航栏和画布两大模块组成。

![explorer-overview](figs/explorer-overview-1.png)

!!! note

    登录 Explorer 后，用户需选择并单击一个目标图空间，才可进入画布页面并解锁左侧导航栏的查询及分析功能。
    
![explorer-overview-graph_space](figs/explorer-overview-graphspace.png)

## 导航栏

用户可以通过单击 Explorer 页面左侧的图标进行图数据导入、图数据分析与探索等操作。左侧导航栏的图标及描述如下：

| 图标  | 描述 |
| ----- | ---- |
| ![query](figs/nav-query2.png) | 输入 VID、Tag 和子图等信息后，匹配的数据会显示到画布上。更多信息，参见[查询方式](ex-ug-query-exploration.md)。     |
| ![filter](figs/nav-filter.png) | 对画布中显示的点进行过滤。更多信息，参见[节点筛选](node-filtering.md)。     |
| ![expand](figs/nav-expand.png) | 选择页面上的节点并进行自定义拓展，包括方向、步数、过滤条件等。    |
| ![commonNeighbor](figs/nav-commonNeighbor.png) | 选择页面上至少两个点并查看它们的共同邻居。     |
| ![findPath](figs/nav-findPath.png) | 查询起点到终点之间的所有路径、最短路径和非循环路径。     |
| ![propertyView](figs/nav-propertyView.png) | 选择是否显画布中的点或边的属性值。     |
| ![hide](figs/nav-miss.png) | 隐藏画布中选中的点边。     |
| ![hideReverse](figs/nav-missReverse.png) | 隐藏画布中未选择的所有点边。     |
| ![Revoke](figs/nav-Revoke.png) | 撤销上一步新增或隐藏的操作。     |
| ![Redo](figs/redo.png) | 恢复上一步撤销的操作。     |
| ![snapshot](figs/snapshot-history.png) | 查看历史快照信息。更多信息，参见[画布快照](canvas-operations/canvas-snapshot.md)。     |
| ![graphSpace](figs/nav-graphSpace.png) | 查看所有图空间，单击图空间可创建相应图空间的画布。     |
| ![Help](figs/nav-help.png) | 查看 Explorer 操作文档、论坛内容等。     |
| ![Setup](figs/nav-setup.png) | 查看用户名和快捷键、修改语言设置、限制返回结果数量、清除 Explorer 链接等。|
| ![Console](figs/nav-console.png) | 使用命令行查询，然后将查询结果导入至画布中。更多信息，参见[控制台](explorer-console.md)。   |

## 画布

图数据可视化地展示在画布中。Explorer 的画布由以下部分组成：

- 顶部页签
- 可视化模式
- 数据存储
- 搜索框
- 多布局
- 缩略图
- 数据概览

更多信息，参见[画布操作](canvas-operations/canvas-overview.md)。
