# 前端技术栈
Vue, Vue-Router, Element-UI, Axios, Echarts

# VSCode常用快捷键

    显示命令面板
    cmd + shift + p

    显示/隐藏侧边栏
    cmd + b

    创建多个编辑器
    cmd + \

    工作区放大/缩小
    cmd + +/-

    控制台显示/隐藏
    cmd + j

    关闭当前文件
    cmd + w

    在多个文件间跳转
    cmd + option + 左/右方向键

    将代码向上/下移动
    option + 上/下方向键

    将代码向上/下复制
    option + shift + 上/下方向键
    
    多光标编辑
    cmd + option + 上/下方向键

    在任意位置同时出现光标
    option + 鼠标点击位置

    删除一行
    cmd + shift + k

    代码格式化
    option + shift + f

    全局搜索
    cmd + shift + f

    搜索文件名
    cmd + p


# 项目初始化
通过脚手架创建项目

    配置路由
    在脚手架创建项目时，添加router

    配置eslint
    在脚手架创建项目时，添加eslint

    配置Element-UI
    在插件中安装，vue-cli-plugin-element

    配置Axios
    在依赖中安装，运行时依赖axios

    配置less,less-loader
    在依赖中安装，开发依赖less,less-loader

    添加第三方字体
    复制素材中的fonts文件夹到assets中，在main.js中导入


处理ESLint警告

    默认情况，ESLint和vscode格式化工具有冲突
    
    在项目根目录下添加 .prettierrc 文件
        {
            "semi":false,
            "singleQuote":true
        }
    
    打开 .eslintrc.js 文件，禁用对 space-before-function-paren 的检查
        fules: {
            ...
            'space-before-function-paren' : 0
        }


# 添加登录

    在components中新建Login.vue组件

    在router中导入组件并配置路由规则

    在assets下新建css文件夹，创建global.css全局样式，并在main.js导入

    Login.vue登录表单使用el-form

    在plugin文件夹打开elements.js，进行elementsui的按需导入

    在main.js导入axios，完成网络配置

    登录完成之后，把token保存到sessionStorage中

    添加路由守卫，没有token的，跳转到登录页


# 首页骨架搭建

    使用el-container,el-header,el-aside,el-main搭建骨架结构

    封装请求拦截器，报文头加上Authorization

# 侧边栏结构

    使用el-menu,el-submenu创建二级菜单模板

    请求侧边栏数据，使用双重v-for绑定菜单模板


# 首页添加子级路由

    创建Welcome子路由，为默认子级路由重定向

    创建Users子路由

# Users组件

    使用面包屑组件el-breadcrumb导航

    卡片视图el-card作为内容区域

    请求用户列表数据

    使用el-table展示用户数据

    添加分页组件el-pagination

    添加新用户el-dialog

# Rights组件

    面包屑组件el-breadcrumb导航

    请求权限列表数据

# Role组件

    面包屑组件el-breadcrumb导航

    请求权限角色数据

    角色的权限数据

    分配角色权限

    分配用户角色


# Goods分类组件

    tree-table使用

    el-cascader级联组件使用

    


    
