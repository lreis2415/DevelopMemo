## 1.Git分支管理

### 1.1.各分支命名规范及功能定义

|             分支             |         命名示例          |                    功能                    |
| :------------------------: | :-------------------: | :--------------------------------------: |
|          `master`          |       `master`        |           稳定版本发布，非工作区，仅管理员可操作            |
|           `dev`            |         `dev`         | 开发汇总，非工作区，开发组成员均可将完成的工作合并至此，当`dev`足够稳定之后，合并至`master` |
|  `bug-<bugcode>-<author>`  | `bug-ompconfused-zlj` | bug修复，临时工作区，修复并测试完成之后，将此分支合并至`dev`，并新建`issue`说明问题之后，删除该分支 |
| `update-<module>-<author>` |   `update-docs-zlj`   |  模块更新，常驻工作区，完成后合并至`dev`、新建`issue`、删除该分支  |
|  `new-<module>-<author>`   |  `new-lisemsed-zlj`   |  新增模块，常驻工作区，完成后合并至`dev`、新建`issue`、删除该分支  |

所以，团队合作的项目分支看起来就像这样：

![分支管理示意图](http://i.imgur.com/Ya2n6vm.jpg)

### 1.2.分支管理一般步骤(以SEIMS模型开发为例)

+ 1.2.1. [加入SEIMS开发组](https://github.com/orgs/lreis2415/teams/watershed_modeling)，直接`clone`实验室账号下的SEIMS库（git@github.com:lreis2415/SEIMS.git）即可，不用`fork`到自己账号下，建议仅克隆`dev`分支，因为默认情况下会克隆所有分支，不仅速度慢，而且浪费空间且没必要，操作如下：

  ```shell
  git clone git@github.com:crazyzlj/SEIMS.git --branch dev --single-branch
  ```

+ 1.2.2. 发现代码中出现了bug、某个文档需要修改、某个模块需要更新、亦或是需要增加一个新的模块等，新建一个分支，修复它，合并至dev分支，并新建issue告知大家，也是备忘，完后删除这个分支，流程如下：

  ```
  # 假设目前cd到SEIMS所在文件夹了
  git checkout -b bug-IFoundU-zlj
  # 修改完成，提交修改
  git add .
  git commit -m "bug fixed of IFoundU"
  # 切换回dev分支，并合并刚才的修改
  git checkout dev
  git merge --no-ff -m "merge from bug-IFoundU-zlj" bug-IFoundU-zlj
  # 如果出现冲突，处理之后，可以删除bug分支了
  git branch -d bug-IFoundU-zlj
  # 去提交一个issue告知大家吧！https://github.com/lreis2415/SEIMS/issues
  ```

+ 1.2.3.删除远程分支。如果某个分支工作已经完成，且已经合并至`dev`分支，在本地可以直接通过`git branch -d tmpbranch`命令删除，远程分支则可推送一个空分支到远程分支，实现删除`git push origin :tmpbranch`