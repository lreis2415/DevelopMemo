# iView 使用记录

- **render** 函数

  ```javascript
  render:(h,params) => {
    return h("定义的页面元素"，{ 属性 }，" 元素的内容"/[子元素])
  }
  ```

  - 定义的页面元素，可以是HTML中的标准元素如 div，也可以是 iview 中的组件，如 Poptip
  - 属性通常包括：用于传递数据的` props:{}`，事件：`on:{}`，样式：`style:{}` ，插槽 `slot`等
  - 元素的内容可以为： 空，文本，子元素。子元素即使只有一个，也需要写成数组形式，即 `[子元素]` 。子元素同样使用 `h("定义的页面元素"，{ 属性 }，" 元素的内容"/[子元素])` 形式

  > https://www.jianshu.com/p/f44a32f83cc8

- table 中**日期格式化**：使用 `render` 函数及 moment 库。如

  ```javascript
  title: 'Create time',
  key: 'groupCreateTime',
  render: ( h, params ) => {
      return h('div', moment(params.row.groupCreateTime).format("YYYY:MM:DD"));
  }
  
  //还可以进行判断
  render: function (h, params) {
     if(this.row.createTime)  
           return h('div', new Date(this.row.createTime).Format('yyyy-MM-dd')); 
     else
           return h('div', ""); 
   }
  ```

- 通过table的slot来实现自定义列 

  > [脱离Table组件的render苦海](https://dev.iviewui.com/articles/1024639493791158272)

  ```javascript
  <!-- 省略部分代码 -->
  <Table ref="myTable" border :data="dataList" :columns="columns">
    <!-- 操作 -->
    <template slot="action" slot-scope="props">
      <Button :disabled="props.age>24" type="primary" size="small">通过</Button>
      <Divider type="vertical" />
      <Poptip confirm title="Delete this item?" transfer>
        <Button type="warning" size="small">删除</Button>
      </Poptip>
    </template>
  </Table>
  
  <!-- 省略部分代码 -->
  render: (h, params) => {
    return h(
      'div',
      this.$refs.myTable.$scopedSlots.action({ age: params.row.age })
    )
  }
  ```
