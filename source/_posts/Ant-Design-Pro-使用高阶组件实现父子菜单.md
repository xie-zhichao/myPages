---
title: Ant Design Pro-使用高阶组件实现父子菜单
date: 2019-06-16 17:35:14
tags:
  - JavaScript
  - React
  - Ant Design
categories: JavaScript
---
## 需求背景
后台管理系统，经常会有：列表+详情+编辑的页面结构，菜单只需展示列表页，详情和编辑，从列表数据进去。

我们想要的效果是：
- 两级页面
- 列表页的面包屑是3级，详情页等是4级，而且可以点击回到3级
- 3级路径时展示列表，4级路径时展示详情等

## 技术设计与实现
### 参考Ant Design Pro示例
首先参考一下Ant Design Pro官方示例：官方示例有一个分步表单的页面，是用的嵌套菜单，不过实现的效果还是单页面结构：
- 只有一级页面
- 面包屑渲染成4级
- 分步表单部分会根据不同的子路径来渲染

示例满足不了我们的需求。

### 自己实现
思路如下：
- 菜单部分，父子菜单的结构，注意：hideChildrenInMenu，在菜单中隐藏子菜单

    ```javascript
        {
            path: '/articles/list',
            name: 'artticle-list',
            component: './article/List',
            hideChildrenInMenu: true,
            routes: [
              {
                path: '/articles/list/detail',
                name: 'artticle-detail',
                component: './article/List/Detail',
              },
            ],
          },
    ```
- 页面部分，用嵌套路由，父路径用exact模式严格匹配，指向父页面组件；子路径使用带参数模式匹配，指向子页面组件。

    > 这里为了方便使用，设计成高阶组件，使用的时候，用装饰器修饰父页面组件即可
    
    父子路由高阶组件定义
    ```javascript
        /**
         * 支持子组件路由的高阶组件
         * @param {Component} WrappedComponent
         */
        export function withChildren(WrappedComponent) {
          class ComponentWithChildren extends PureComponent {
            render() {
              const { children, match } = this.props;
              return (
                <React.Fragment>
                  <Route
                    path={`${match.url}/:action`}
                    render={() => <React.Fragment>{children}</React.Fragment>}
                  />
                  <Route exact path={match.url} render={() => <WrappedComponent {...this.props} />} />
                </React.Fragment>
              );
            }
          }
        
          return ComponentWithChildren;
        }
    ```
     父子路由高阶组件使用
    ```javascript
        @withChildren
        class TableList extends PureComponent {
        
            // ...
        }
    ```
到此完美实现我们的需求。