---
layout: post
title: 依赖声明- F.I.S
category: advance
---

# 依赖声明

<i class="anchor" id='html'></i>

## 在html中声明依赖

> 用户可以在html的注释中声明依赖关系，这些依赖关系最终会被记录到fis编译产出的 **map.json** 文件中。

在项目的index.html里使用注释声明依赖关系：

```html
<!--
    @require demo.js
    @require "demo.css"
-->
```

由于默认情况下，只有js和css文件可以被录入map.json表中，如果想将html文件加入表中，需要添加配置声明html文件为模块化文件，例如：

```javascript
//fis-conf.js
fis.config.merge({
    roadmap : {
        path : [
            {
                //所有的html文件
                reg : '**.html',
                //都是组件化文件
                isMod : true
            }
        ]
    }
});
```

执行 fis release --dest ./output --md5 命令对项目进行编译，查看output目录下的map.json文件，则可看到：

```json
{
    "res" : {
        "demo.css" : {
            "uri" : "/static/css/demo_7defa41.css",
            "type" : "css"
        },
        "demo.js" : {
            "uri" : "/static/js/demo_33c5143.js",
            "type" : "js",
            "deps" : [ "demo.css" ]
        },
        "index.html" : {
            "uri" : "/index.html",
            "type" : "html",
            "deps" : [ "demo.js", "demo.css" ]
        }
    },
    "pkg" : {}
}
```

<i class="anchor" id='js'></i>

## 在js中声明依赖

> fis支持识别js文件中的 **require函数**，或者 **注释中的@require字段** 标记的依赖关系，这些分析处理对 **html的script标签内容** 同样有效。

```javascript
//demo.js
/**
 * @require demo.css
 * @require list.js
 */
var $ = require('jquery');
```

经过编译之后，查看产出的 **map.json** 文件，可以看到：

```json
{
    "res" : {
        ...
        "demo.js" : {
            "uri" : "/static/js/demo_33c5143.js",
            "type" : "js",
            "deps" : [ "demo.css", "list.js", "jquery" ]
        },
        ...
    },
    "pkg" : {}
}
```

<i class="anchor" id='css'></i>

## 在css中声明依赖

> fis支持识别css文件 **注释中的@require字段** 标记的依赖关系，这些分析处理对 **html的style标签内容** 同样有效。

```css
/**
 * demo.css
 * @require reset.css
 */
```

经过编译之后，查看产出的 **map.json** 文件，可以看到：

```json
{
    "res" : {
        ...
        "demo.css" : {
            "uri" : "/static/css/demo_7defa41.css",
            "type" : "css",
            "deps" : [ "reset.css" ]
        },
        ...
    },
    "pkg" : {}
}
```