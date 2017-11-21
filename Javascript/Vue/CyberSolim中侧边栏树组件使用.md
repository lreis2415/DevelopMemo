## CyberSolim中侧边栏树组件使用

>   侯志伟 2017年11月21日 待更

## 前端

Vue 组件 `cybersolim-frontend\src\components\widgets\tree` 

使用实例 `cybersolim-frontend\src\components\data\projects\ProjectsTree.vue`

```js
import VueTree from '../../widgets/tree/Tree'
  
// data 的 return 中
//右键菜单项
 contextMenus: [{
     text: 'New project',
     click: node => {
        console.log(node.id); // 本结点
     }
  }],
  buttons: [{
      key: 'detail', // 后台数据需要提供一个对应的key列表
      title: 'Show detail',
      icon: 'fa fa-table',
      click: node => {
            // 数据表格面板
            this.$store.dispatch(actionTypes.SHOW_PROJECT_DETAIL, true);
            this.$store.dispatch(actionTypes.PROJECT_DETAIL_ID, node.id);
       }
   },
   {
       key: 'map',
       text: 'Show on map',
       icon: 'fa fa-map',
       click: node => {
           console.log(node);
           vIziToast.success(node.id);
         }
  }]
```

### 后台

`cybersolim-web\src\main\java\org\egc\cybersolim\controller\ProjectController.java`

```java
//引用commons中的类
import org.egc.commons.web.tree.SidebarTree;
import org.egc.commons.web.tree.SidebarTreeNode;

 // 返回树形组件数据
    @GetMapping("/listTree")
    public JsonResult listProjectTree() {
        int userId = userService.getUserIdFromToken(token);
        List<Project> projects = projectService.findProjects(userId);
        SidebarTree tree = new SidebarTree("Projects",true); // 根节点，名称为 Projects
        List<SidebarTreeNode> nodeList = new ArrayList<>();

        for (Project project : projects) {
            SidebarTreeNode node = new SidebarTreeNode(0);// 以根节点（id为0）为父节点
            node.setTitle(project.getProjectName());
            node.setName(project.getProjectName());
            node.setId(project.getProjectId());
            String[] buttons = {"detail"}; // 与上面buttons中的key对应
            node.setButtons(buttons);
            // 如果有数据集，则设置 setChildren 并  String[] buttons = {"detail"};
            // 如果数据集有空间数据，则空间数据项添加展示地图按钮 String[] buttons = {"map"};
            nodeList.add(node);
        }
        tree.setChildren(nodeList);
        json.setData(tree);
        return json;
    }

```



