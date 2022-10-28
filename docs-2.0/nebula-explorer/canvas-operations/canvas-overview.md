# 画布概览

用户可在画布中可视化地探索图数据。本文主要介绍画布的组成及相关功能。

画布概览图如下：

![canvas](https://docs-cdn.nebula-graph.com.cn/figures/canvas-overview-220712-cn.png)

## 顶部页签

用户可以通过单击画布顶部加号![canvas_tab](https://docs-cdn.nebula-graph.com.cn/figures/list-add_cn.png)图标同时操作多个画布。

![canvas_overview](https://docs-cdn.nebula-graph.com.cn/figures/canvas-graphspace-22-04-06_cn.png)

- 不同画布上的数据可以来自同一个图空间也可以来自不同的图空间。
- 除了最左边页签的第一个画布外，用户可以自定义画布的名称。

## 可视化模式

用户可以选择 **2D 模式**和 **3D 模式**可视化地在画布上探索图数据。更多信息，参见[可视化模式](visualization-mode.md)。

## 数据存储

用户可以通过创建快照，或以图片、CSV 文件的方式导出画布以存储当前画布数据。

在页面右上方：

- 单击![snapshot](https://docs-cdn.nebula-graph.com.cn/figures/graph-snapshot_cn.png)创建快照。更多信息，参见[画布快照](canvas-snapshot.md)。

- 单击![data_output](https://docs-cdn.nebula-graph.com.cn/figures/explorer-btn-output.png)，然后单击**导出 CSV** 以 CSV 文件的方式存储画布数据。

- 单击![data_output](https://docs-cdn.nebula-graph.com.cn/figures/explorer-btn-output.png)，然后单击**导出 PNG** 以图片形式存储画布数据。

## 搜索框

在页面右上方，单击![canvas_search](https://docs-cdn.nebula-graph.com.cn/figures/explorer-canvas-search.png)，然后在搜索框中输入 VID 或者 Tag 的属性值定位目标点。

## 多布局

Explorer 支持 6 种布局方式展示画布上的数据之间的关系。

| 力导向图 | 层次图 | 环形图 | 网格  | 神经网络 | 辐射  |
| -------- | ------ | ------ | ----- | -------- | ----- |
| ![graphView](https://docs-cdn.nebula-graph.com.cn/figures/Thumbnail-graphView_cn.png)    | ![treeView](https://docs-cdn.nebula-graph.com.cn/figures/Thumbnail-treeView_cn.png)  | ![sphereView](https://docs-cdn.nebula-graph.com.cn/figures/Thumbnail-sphereView_cn.png)  | ![grid](https://docs-cdn.nebula-graph.com.cn/figures/Thumbnail-Grid_cn.png) | ![neural](https://docs-cdn.nebula-graph.com.cn/figures/Thumbnail-neuralNetwork_cn.png)    | ![radial](https://docs-cdn.nebula-graph.com.cn/figures/Thumbnail-Radial_cn.png) |

![layouts](https://docs-cdn.nebula-graph.com.cn/figures/layout-22-04-06_cn.gif)

## 缩略图

全屏展示画布中的图，收起缩略图，缩小或放大画布中的图等。同时在缩略图的左下角显示了画布中的图占总图的百分比。

![](https://docs-cdn.nebula-graph.com.cn/figures/thumbnail_cn.png)

## 数据概览

在页面右侧，单击![list-left](https://docs-cdn.nebula-graph.com.cn/figures/list-left_cn.png)展开数据概览面板。

![dataView](https://docs-cdn.nebula-graph.com.cn/figures/dataview_cn.png)

- 可以在数据概览面板中看到画布中的 Tag 数、Edge type 数，及对应的点和边的数量。
- 在数据概览面板，单击 Tag 或 Edge type 颜色图标，可自定义颜色、尺寸，Tag 还支持自定义图标、图片。

  !!! note
        数据概览面板中只支持批量修改颜色。在画布中右键单击单个点可以手动修改单个点的样式。

- 支持上传图片以个性化标记画布中的点的样式，上传的图片被存在浏览器中。如果需要永久保存上传的图片，可以将当前画布中的数据创建为快照，具体操作，参见[数据快照](canvas-snapshot.md)。

  ![upload_logo](https://docs-cdn.nebula-graph.com.cn/figures/upload-logo_cn.png)


选中画布中的点和边，然后在画布左下角单击**选中的点 {number}，选中的边 {number}**查看选中的点或边的数据信息并以 CSV 文件方式导出数据。

![view-data](https://docs-cdn.nebula-graph.com.cn/figures/view-selected-data-22-04-06_cn.png)
