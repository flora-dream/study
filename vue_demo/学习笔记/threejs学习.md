## 一、npm安装相关依赖
1.安装threejs
`npm i three`

2.安装threejs代码提示
`npm i @types/three -D` 安装到开发环境
## 二、在vue文件中引用threejs
```js
<script>
    import * as THREE from "three"; // 引入threejs
<script>
```

## 三、导入控制器
控制器需要单独导入
```js
<script>
    import * as THREE from "three"; // 引入threejs
    import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"; // 引入控制器
</script>
```
## 四、打开案例跨域问题
通常一个threejs项目案例往往都会加载一些外部模型，因此打开threejs案例要搭建一个本地的静态服务器，否则的话，threejs案例无法正常打开，浏览器控制台会提示跨域问题。
### Nodejs本地静态服务器
执行npm install -g live-server安装live-server模块，如果你想通过安装好的live-server模块开启一个静态服务器，打开命令行，进入threejs案例所在的文件目录，然后执行live-server命令就可以。