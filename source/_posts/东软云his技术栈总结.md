---
title: 东软云his技术栈总结
date: 2023-12-10 21:05:00
update: 2023-12-10 21:05:00
tags: java, vue, springboot, mybatis, mysql
cover: https://images5.alphacoders.com/124/thumbbig-1240551.webp
---

# 前言
本篇文章旨在记录大四小组实习实训期间项目所使用的技术栈，以及所使用的框架，框架的优缺点，以及框架的原理。同时对于在开发项目的过程中所遇到的问题和一些解决方案，以及一些技术点进行总结。

# 1、项目主要技术栈以及开发工具
## 1.1、前端技术栈
前端主要使用vue技术栈，vue技术栈的优点在于组件化，组件化使得代码可复用性高，组件化使得代码可维护性高，组件化使得代码可扩展性高。

## 1.2、后端技术栈
后端开发语言选择java，并主要使用springboot技术栈，springboot技术栈的优点在于快速开发，快速开发使得开发效率高，快速开发使得部署效率高，快速开发使得维护效率高。

## 1.3、数据库     
数据库使用mysql，mysql的优点在于开源，免费，使用成本低，mysql的缺点在于性能差，mysql的缺点在于数据量大的时候性能差。但对于该项目而言，mysql的优点大于缺点，已足够使用。

## 1.4、开发工具
开发工具使用：IntelliJ IDEA 2023.2.3，vscode，MySQL Workbench等。
***
# 2、架构设计
## 2.1、前后端分离架构设计
前后端分离架构设计，前端使用vue技术栈，后端使用springboot技术栈，前端和后端通过接口进行交互。

## 2.2、业务架构设计
业务架构设计，主要分为一下几个部分：
- 用户管理模块
- 患者管理模块
- 医生管理模块
- 科室管理模块
- 挂号管理模块
- 处方管理模块
- 药房管理模块
具体就是分为基础信息维护、门诊挂号收费、门诊医生工作站、门诊医技工作站、门诊财务管理等。
***
# 3、模块说明
## 3.1、 前端模块说明
- 使用多种vue组件，实现了上述功能
- 引入动态路由，实现不同角色的权限分离，每个身份对应不用的Vue组件和路由
- 添加路由卫士，实现所有路由数据均由后端返回

## 3.2、 后端模块说明
- config:配置跨域支持，并且添加了MyBatis-Plus的分页插件，以便在数据库查询中使用分页功能
- controller: 控制层包括医生、医技、动态菜单等业务逻辑的实现
- entity: 实体层包括项目涉及到的所有类的定义
- mapper: 数据层，用于与数据库的操作
- service: 实现与前端交互
- util: 一些自定义的工具类
***
# 4、实现细节
## 4.1、 前端主要部分举例
### 4.1.1、 路由
1、导入模块。
"Vue"和"VueRouter"分别是Vue.js核心库和Vue Router库
axios是一个用于发起HTTP请求的库，axios支持Promise，能轻松实现异步请求。store是VueX的数据存储仓库。
qs是一个用于序列化和解析URL参数的库。

2、使用插件
使用 VueRouter 插件，使 Vue 应用具备路由功能。
将 qs 库挂载到 Vue 原型上，以便在组件中使用。
```javascript
Vue.use(VueRouter)
Vue.prototype.$qs = qs;
```

3、定义路由
定义一些路由规则，包括默认重定向、主页路径和登录路径。
```javascript
const routes = [
  // ... (路由配置)
]
```

4、创建路由实例
创建一个路由示例
```javascript
const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})
```

5、路由守卫
使用 beforeEach 路由守卫，用于在切换路由前执行一些逻辑。
在这里，判断是否有路由，如果没有，则从后端获取用户权限信息，并动态添加路由。
```javascript
router.beforeEach((to, from, next) => {
  // ... (路由守卫的逻辑)
})
```

6、导航转为路由
定义了一个函数 menuToRoute 用于将菜单项转换为路由。
```javascript
const menuToRoute = (menu) => {
  // ... (导航转成路由的逻辑)}
```

7、导出路由示例
导出创建好的路由实例，以便在 Vue 应用中使用。
```javascript
export default router
```

### 4.1.2、 登录界面
使用Vue.js结合Element UI实现了用户登录功能。通过Vue.js和Element UI提供的组件和功能，实现了用户登录的表单验证和提交功能。Axios用于和后端进行数据交互，而Vuex则用于管理全局状态，包括用户Token信息。通过路由守卫实现了在用户登录成功后进行路由跳转。

### 4.1.3、 主页
基于Vue.js和Element UI的前端页面，主要用于展示医疗系统的主界面，包括侧边栏、头部导航、用户信息和主体内容。
使用Vue.js构建前端单页面应用。利用Element UI组件库，例如el-container、el-aside、el-header、el-main等，来构建页面布局和各种UI元素。
利用Vue.js的组件化开发思想，将页面划分为多个组件，例如SideMenu、Tabs等，提高了代码的可维护性和可读性。
利用Vue Router进行前端路由管理，通过<router-view>标签动态加载视图组件。
使用Vuex进行状态管理，存储和管理全局状态，如用户信息。利用Element UI的栅格系统进行响应式布局，确保在不同屏幕尺寸下能够正常显示。

### 4.1.4、 侧边栏菜单
1、**模板部分**
使用了el-menu组件，设置了一些样式和属性，包括默认激活的菜单项、文本颜色、背景颜色等。
使用v-for指令遍历menuList，生成侧边栏菜单。menuList从Vuex中获取，即this.$store.state.menus.menuList。
使用router-link包裹el-menu-item，实现点击菜单项跳转到相应的路由。

2、**脚本部分**
组件的名称为SideMenu。
在computed中使用计算属性menuList获取菜单列表，该菜单列表存储在Vuex的menus模块中的menuList属性中。

3、**方法部分**
selectMenu方法用于处理菜单项的点击事件，通过$store.commit调用addTab mutation，将点击的菜单项添加到标签页中。
总体而言，实现了基于Element UI的垂直侧边栏菜单。菜单项来自于Vuex中的menuList，并且支持点击菜单项后在主体区域打开相应的路由页面。通过这种模块化的设计，可以方便地扩展和管理系统的菜单功能。

### 4.1.5、 标签页
1、**模板部分**
使用了el-tabs组件，设置了一些属性，包括标签页的类型为card卡片形式、可关闭标签页、监听标签页的关闭事件（@tab-remove）和标签页的点击事件（@tab-click）。
使用v-for指令遍历editableTabs数组，生成可编辑的标签页。

2、**脚本部分**
组件的名称为Tabs。
在data中没有声明任何局部状态，数据都是通过computed属性来获取和设置，这些数据来自于Vuex中的menus模块的editableTabs和editableTabsValue属性。
提供了两个计算属性：editableTabs和editableTabsValue，分别获取和设置Vuex中的相关数据。

3、**方法部分**
removeTab方法用于处理标签页的关闭事件。在关闭标签页时，会更新editableTabs和editableTabsValue的值，并通过this.$router.push方法跳转到对应的路由页面。若关闭的是首页（'Index'），则直接返回不做任何操作。
clickTab方法用于处理标签页的点击事件。在点击标签页时，同样会通过this.$router.push方法跳转到对应的路由页面。

总体而言，实现了展示可编辑的标签页，标签页的内容由el-tab-pane组件承载，与路由进行关联，通过Vuex中的状态管理实现了标签页的动态增删和路由切换。

这里只节选部分功能进行说明。

## 4.2 后端主要部分举例
### 4.2.1、 登录Controller
这个控制器主要处理用户登录和登出的请求，通过注解映射相应的URL，并调用UserService处理业务逻辑，返回相应的结果给前端。

1、注解和依赖注入
@RestController：表明这是一个RESTful风格的控制器，返回的数据都是以JSON格式。@Autowired：自动注入UserService实例。

2、RequestMapping注解
@RequestMapping("/login")：映射处理来自前端的/login请求。
@RequestMapping("/logout")：映射处理来自前端的/logout请求。

3、login方法
处理用户登录请求。
使用@RequestMapping("/login")注解，接收前端传递的User对象参数。
打印接收到的前端数据和执行了登录的提示信息。
调用UserService的login方法，返回登录用户的信息。
判断登录用户是否为null，如果不为null，返回登录成功的结果；否则返回登录失败的结果。

4、logout方法
处理用户登出请求。
使用@RequestMapping("/logout")注解，接收前端传递的参数。
直接返回登出成功的结果。

5、返回方法
使用ResultUtil类构建返回结果，其中包括响应码（EnumCode）和消息。
登录成功时返回用户信息，登录失败和登出成功时只返回消息。

### 4.2.2、 用户Controller
这个控制器主要处理关于用户信息的查询请求，通过注解映射相应的URL，并调用UserService处理业务逻辑，返回相应的结果给前端。这样的设计使得代码更加模块化，实现了前后端的分离。

1、注解和依赖注入
@RestController：表明这是一个RESTful风格的控制器，返回的数据都是以JSON格式。
@Autowired：自动注入UserService实例。

2、RequestMapping注解
@RequestMapping("/queryUserAll")：映射处理来自前端的/queryUserAll请求。
@RequestMapping("/queryUserDeptByID")：映射处理来自前端的/queryUserDeptByID请求。

3、getUserAll方法
处理查询所有用户信息的请求。
使用@RequestMapping("/queryUserAll")注解，接收前端传递的User对象参数。
调用UserService的selectAll方法，返回所有用户的信息列表。
判断用户列表是否为null，如果不为null，返回查询成功的结果；否则返回查询失败的结果。

4、getUserDeptByID方法
处理根据用户ID查询用户所在部门信息的请求。
使用@RequestMapping("/queryUserDeptByID")注解，接收前端传递的User对象参数。
调用UserService的selectDeptByID方法，返回指定用户所在部门的信息。
判断部门信息是否为null，如果不为null，返回查询成功的结果；否则返回查询失败的结果。

这里也同样只挑出来一些比较有代表性的功能实现，还有其它一些功能这里就不再赘述了。
***
# 5、测试
测试用浏览器：Chrome 113.0.5672.127
测试工具：Selenium(用于自动化浏览器操作的工具集和库，主要用于Web应用程序的测试，支持多种浏览器和多种操作系统)
测试报告框架：Allure(一种用于测试报告生成和测试结果可视化的开源测试报告框架)

# 6、遇到的问题挑战
小组各成员在项目初期均对java不够熟悉，同时对于各种前后端框架的使用也不熟悉，学习成本比较高，在此处花费了大量的时间。同时该项目比较考验小组的协作分工能力，在项目初期成员分工不明确，导致项目进度比较缓慢，后期成员分工比较明确，项目进展比较顺利。

# 7、项目总结
通过这次小组合作项目，我们学到了很多东西，包括前后端分离、SpringBoot、SpringMVC、MyBatis、MySQL、Maven、Ajax、JSON、Javascript等技术，也提高了我们团队协作的能力，同时锻炼了我们独立思考的能力，相信对小组的各成员今后成长都有益处。