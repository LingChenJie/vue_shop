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

    


# 去除空格限制
    
    新建.prettierrc文件
    

    在.eslintrc.js添加rules
    'space-before-function-paren' : 0

