# How to Use Angular Material 

### 安装 Angular Material

使用 Angular CLI 的安装[原理图](https://material.angular.cn/guide/schematics)通过运行以下命令来设置你的 Angular Material 项目：

```
ng add @angular/material
```

该 `ng add` 命令将安装 Angular Material、[组件开发工具包（CDK）](https://material.angular.cn/cdk/categories)、[Angular 动画](https://angular.cn/guide/animations)，并询问下列问题，以确定要包括哪些功能：

1. 选择一个预置的主题名或为自定义主题选择 “custom”：

   你可以选择一个[预置的 Material Design 主题](https://material.angular.cn/guide/theming#using-a-pre-built-theme)或建立一个可扩展的[自定义主题](https://material.angular.cn/guide/theming#defining-a-custom-theme)。

2. 设置全局 Angular Material 排版样式：

   是否为应用程序套用全局[排版](https://material.angular.cn/guide/typography)样式。

3. 为 Angular Material 设置浏览器动画：

   把 [`BrowserAnimationsModule`](https://angular.cn/api/platform-browser/animations/BrowserAnimationsModule)导入到应用中，可以启用 Angular 的[动画体系](https://angular.cn/guide/animations)。拒绝它会使 Angular Material 中的动画失效。

`ng add` 命令还将执行以下配置：

- 把项目依赖加入到 `package.json` 中
- 把 Roboto 字体添加到你的 `index.html` 中
- 把 Material Design 的图标字体添加到你 `index.html` 中
- 添加一些全局 CSS 样式：
  - 去掉 `body` 的边距
  - 为 `html` 和 `body` 设置 `height: 100%`
  - 把 Roboto 设置为默认的应用字体

完工！ Angular Material 已经配置好应用于你的应用中了。

### 显示一个组件

让我们在应用中显示一个滑块组件，来验证一切正常。

你需要通过把以下代码添加到 app.module.ts 文件中来导入 `MatSliderModule`。

```
import { MatSliderModule } from '@angular/material/slider';
…
@NgModule ({....
  imports: [...,
  MatSliderModule,
…]
})
```

把 `<mat-slider>` 标签添加到 `app.component.html`，就像这样：

```html
<mat-slider min="1" max="100" step="1" value="1"></mat-slider>
```

运行本地开发服务器：

```
ng serve
```

让浏览器访问 [http://localhost:4200](http://localhost:4200/)

你会在页面上看到这个 Material 滑块组件。

除了安装原理图之外，Angular Material 还带有[另外一些原理图](https://material.angular.cn/guide/schematics)（如导航、表格、地址表单等），可用于方便地在应用中生成预置的组件。

# 原理图

Angular Material 带了一份供 Angular CLI 使用的原理图（schematic），可以让创建 Material 应用更简单些。

### 安装原理图

Angular Material 附带了一些原理图，一旦你安装了这个 npm 包，就可以通过 Angular CLI 来使用这些原理图了。

运行下列命令可以自动为你的项目安装 Angular Material、[组件开发工具](https://material.angular.cn/guide/cdk) (CDK) 和 [Angular 动画库](https://material.angular.cn/guide/animations)，并运行代码原理图

Angular Material 的 `ng add` 原理图会帮助你的 Angular CLI 项目配置 Angular Material。运行 `ng add` 将会：

- 确保 `package.json` 中具有[项目所需的依赖](https://material.angular.cn/guide/getting-started#step-1-install-angular-material-angular-cdk-and-angular-animations)
- 为应用启用 [BrowserAnimationsModule](https://material.angular.cn/guide/getting-started#step-2-configure-animations) 模块
- 添加一个[预定义的主题](https://material.angular.cn/guide/theming#using-a-pre-built-theme)或一个[自定义主题](https://material.angular.cn/guide/theming#defining-a-custom-theme)
- 为 `index.html` 添加 Roboto 字体
- 在 `index.html` 中添加 [Material 图标字体](https://material.angular.cn/guide/getting-started#step-6-optional-add-material-icons)
- 添加下列全局样式
  - 移除 `body` 的边距（margin）
  - 为 `html` 和 `body` 设置 `height: 100%`
  - 把 Roboto 设置为应用的默认字体
- 安装并导入 `hammerjs`，来为项目提供[手势支持](https://material.angular.cn/guide/getting-started#step-5-gesture-support)

## 组件原理图

除了用于安装的 schematic 之外，Angular Material 还带有多个原理图，用于轻松生成 Material Design 组件：

| 名称           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| `address-form` | 一个表单组，它使用 Material Design 的多个表单控件接收一个送货地址 |
| `navigation`   | 创建一个带有响应式 Material Design 侧边栏组件和一个用于显示应用名称的工具栏组件 |
| `dashboard`    | 带有多个 Material Design 卡片和菜单的网格式布局组件          |
| `table`        | 生成一个带有 Material Design 并支持排序和分页的数据表组件    |
| `tree`         | 该组件以 `<mat-tree>` 组件来展示一个可交互的文件夹式结构     |

此外，Angular CDK 也给出了一些组件的原理图：

| 名称        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| `drag-drop` | 该组件使用 `@angular/cdk/drag-drop` 指令来创建一个可交互的 to-do 列表 |

#### 地址表单原理图

运行 `address-form` 原理图会生成一个新的 Angular 组件，它可用于快速开始一个 Material Design 表单组，其中包括：

- 一些 Material Design 表单字段
- 一些 Material Design 单选控件
- 一些 Material Design 按钮

```
ng generate @angular/material:address-form <component-name>
```

####  导航原理图

`navigation` 原理图将会创建一个包括应用名的工具栏和一个能自适应 Material 的断点的侧边栏。

```
ng generate @angular/material:navigation <component-name>
```

####  表格原理图

表格原理图将创建一个组件，它可以渲染出一个预置了可排序、可分页数据源的 Angular Material `<table>`。

```
ng generate @angular/material:table <component-name>
```

####  仪表盘原理图

`dashboard` 原理图将创建一个新组件，它包含一个由 Material Design 卡片组成的动态网格列表。

```
ng generate @angular/material:dashboard <component-name>
```

####  树原理图

`tree` 原理图可用于快速生成一个 Angular 组件，它使用 Angular Material 的 `<mat-tree>` 组件来展示一个嵌套的文件夹式结构。

```
ng generate @angular/material:tree <component-name>
```

####  拖放原理图

`drag-drop` 原理图是由 `@angular/cdk` 提供的，它可用来生成带有 CDK 拖放指令的组件。

```
ng generate @angular/cdk:drag-drop <component-name>
```