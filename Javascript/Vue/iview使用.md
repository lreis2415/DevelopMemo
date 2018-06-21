# iView 使用记录

- **render** 函数

  ```javascript
  render:(h,params) => {
    return h("定义的页面元素"，{ 属性 }，" 元素的内容"/[子元素])
  }
  ```

  - 定义的页面元素，可以是HTML中的标准元素如 div，也可以是 iview 中的组件，如 Poptip
  - 属性通常包括：用于传递数据的` props:{}`，事件：`on:{}`，样式：`style:{}` 等
  - 元素的内容可以为： 空，文本，子元素。子元素即使只有一个，也需要写成数组形式，即 `[子元素]` 。子元素同样使用 `h("定义的页面元素"，{ 属性 }，" 元素的内容"/[子元素])` 形式

  > https://www.jianshu.com/p/f44a32f83cc8

- table 中**日期格式化**：使用 `render` 函数及 moment 库。如

  ```javascript
  title: 'Create time',
  key: 'groupCreateTime',
  render: ( h, params ) => {
      return h('div', moment(params.row.groupCreateTime).format("YYYY:MM:DD"));
  }
  ```

  