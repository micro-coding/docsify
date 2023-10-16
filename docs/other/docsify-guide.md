# Docsify 建站指南

docsify 是一款文档网站生成器，可以帮助用户快速生成文档网站。官网: <https://docsify.js.org/#/zh-cn/>

## 项目初始化

安装 docsify-cli

```bash
npm i docsify-cli -g
```

如果想在项目的 ./docs 目录里写文档，直接通过 init 初始化项目。

```bash
docsify init ./docs
```

项目目录结构如下:

```file
docs
  .nojekyll   //用于阻止 GitHub Pages 忽略掉下划线开头的文件
  index.html  //入口文件，承载项目配置
  README.md   //用于主页内容渲染
```

运行 docsify serve 启动一个本地服务器，可以方便地实时预览效果。默认访问地址 <http://localhost:3000> 。

```bash
docsify serve docs
```

## 侧边栏

配置 loadSidebar 选项，开启侧边栏。

```html
<!-- index.html -->

<script>
  window.$docsify = {
    loadSidebar: true
  }
</script>
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```

创建文件 `_sidebar.md`，定制侧边栏。

```md
<!-- docs/_sidebar.md -->

* [首页](zh-cn/)
* [指南](zh-cn/guide)
```

侧边栏优先读取当前目录下`_sidebar.md`文件，当前目录不存在，则读取上一级目录。因此，为每个文件夹创建一个`_sidebar.md`文件，可以使侧边栏只显示当前目录。

配置`alias` 可以避免不必要的回退查询。

```html
<script>
  window.$docsify = {
    loadSidebar: true,
    alias: {
      '/.*/_sidebar.md': '/_sidebar.md'
    }
  }
</script>
```

?> 可以在一个子目录中创建一个 `README.md` 文件来作为路由的默认网页

## 导航栏

配置 loadNavbar，创建文件`_navbar.md`，定制导航栏。

```html
<!-- index.html -->

<script>
  window.$docsify = {
    loadNavbar: true
  }
</script>
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```

```md
<!-- _navbar.md -->

* [En](/)
* [简体中文](/zh-cn/)
```

`_navbar.md` 加载逻辑和 sidebar 文件一致，从每层目录下获取。例如当前路由为 `/zh-cn/custom-navbar` 那么是从 `/zh-cn/_navbar.md` 获取导航栏。

`_navbar.md`中的列表会被渲染为嵌套的下拉列表菜单。

```md
<!-- _navbar.md -->

* 入门

  * [快速开始](zh-cn/quickstart.md)
  * [多页文档](zh-cn/more-pages.md)
* 配置
  * [配置项](zh-cn/configuration.md)
  * [主题](zh-cn/themes.md)
```

## 封面

配置 coverpage，创建文件`_coverpage.md`,自定义封面，支持自定义背景图片和背景色。

```html
<!-- index.html -->

<script>
  window.$docsify = {
    coverpage: true
  }
</script>
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```

```md
<!-- _coverpage.md -->

![logo](_media/icon.svg)

# docsify <small>3.5</small>

[GitHub](https://github.com/docsifyjs/docsify/)
[Get Started](#docsify)

<!-- 背景图片 -->

![](_media/bg.png)

<!-- 背景色 -->

![color](#f0f0f0)
```

设置 onlyCover，只在访问主页时加载封面。

```html
window.$docsify = {
  onlyCover: false,
};
```

## 配置项

配置文档站名称

```html
`name:"docsify"
```

配置仓库地址

```html
repo: 'https://github.com/docsifyjs/docsify/'
```

切换页面后是否自动跳转到页面顶部

```html
auto2top: true
```

滚动到指定的锚点时，保留距页面顶部空间

```html
topMargin: 40
```

自定义侧边栏后默认不会再生成目录，可以通过设置生成目录的最大层级开启这个功能

```html
subMaxLevel: 3
```

默认抓取文档中所有标题渲染成目录，可配置最大支持渲染的标题层级

```html
maxLevel: 4
```

## 推荐插件

支持emoji

```html
<script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/emoji.min.js"></script>
```

图片缩放

```html
<script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/zoom-image.min.js"></script>
```

复制到剪贴板

```html
<script src="//cdn.jsdelivr.net/npm/docsify-copy-code/dist/docsify-copy-code.min.js"></script>
```

代码高亮

```html
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-bash.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-sql.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-java.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-go.min.js"></script>
```

## 项目部署

推送项目到 Github，在项目 Settings 下的 Github Pages 部分，选择源分支和目录，保存后访问网站。

参考资料：

* [docsify](https://docsify.js.org/#/zh-cn/quickstart)
