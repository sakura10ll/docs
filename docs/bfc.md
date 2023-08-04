#### BFC是什么

BFC 是 Block Formating Context 的简写，可将其翻译成"块级格式化上下文"。它会创建一个特殊的区域，在这个区域中，只有block box 参与布局。而BFC的一系列特点和规则规定了在这个特殊的区域中如何进行布局，如何进行定位，区域内元素的相互关系和相互作用是怎样的。这个特殊的区域不受外界影响。
block box 是指 display 属性为 block、list-item、table 的元素。
- - - - 

#### 如何形成 BFC

什么样的元素会创建一个BFC呢？MDN对此的总结如下：
- 文档的根元素(`<html>`)
- 浮动元素(元素的 `float` 不为 `none`)
- 绝对定位元素(元素的 `position` 为 `absolute` 或 `fixed`)
- 行内块元素(元素具有 `display:inline-block` 属性)
- 表格单元格(元素具有 `display:table-cell`，HTML 表格单元格默认属性)
- 表格标题(元素具有 `display:table-caption`，HTML 表格标题默认属性)
- 匿名表格单元格元素
- 具有 `overflow` 且值不为 `visible` 或 `clip` 的块元素
- 含有样式属性 `display:flow-root` 的元素
- 具有 `contain` 且值为 `layout`、`content` 或 `paint` 的元素
- 弹性元素(`display` 值为 `flex` 或 `inline-flex `元素的直接子元素)
- 网格元素(`display` 值为 `grid` 或 `inline-grid` 元素的直接子元素)
- 多列容器（`column-count` 或 `column-width` (en-US) 值不为 `auto`，且含有 `column-count: 1` 的元素）
- 含有样式属性 `column-span: all` 的元素

- - - 

#### BFC 决定了什么
- 内部的 box 将会独占宽度，且在垂直方向上一个接一个排列
- box 在垂直方向上的间距由 `margin` 属性决定，但是同一个 `BFC` 的两个相邻 box 的 `margin` 会出现边距折叠现象。
- 每个 box 在水平方向上的左边缘与 `BFC` 的左边缘相对齐，即使存在浮动也是如此。
- `BFC` 区域不会与浮动元素重叠，而是会依次排列。
- `BFC` 区域是一个独立的渲染容器，容器内的元素和 `BFC` 区域外的元素之间不会有任何干扰。
- 浮动元素的高度也参与 `BFC` 的高度计算。 

>格式化上下文影响布局，通常，我们会为定位和清除浮动创建新的 BFC，而不是更改布局，因为它将：
>- 包含内部浮动。
>- 排除外部浮动。
>- 阻止外边距重叠。
