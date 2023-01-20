---
title: float布局及清除浮动
date: {{ date }}
tags:
 - 前端
 - css
categories: 
 - css
cover: /image/cover2.jpg
---
## 1.float属性
> left - 元素浮动到容器左侧
  right - 元素浮动到容器右侧
  none - 元素不浮动，默认值
  inherit - 元素继承其父级的float值

## 2.实现圣杯布局
要求如下：
> 三栏布局，中间一栏最先加载和渲染
  两侧内容固定，中间内容随着宽度自适应

{% tabs 圣杯布局 %}
<!-- tab 初始代码 -->
``` HTML
<body>
    <div id="container">
        <div class="center">这是一段内容，显示中间，自适应，优先加载 </div>
        <div class="left">this is left</div>
        <div class="right">this is right</div>
    </div>
    <div class="footer">this is footer</div>
</body>
<style>
    .center {
        background-color: #ffa;
        width: 100%;
    }
    .left {
        width: 300px;
        background-color: #faf;
    }
    .right {
        width: 300px;
        background-color: #afa;
    }
    .footer {
        background-color: #aaa;
    }
</style>
```
<!-- endtab -->
<!-- tab 渲染效果 -->
![](/image/textImg/float-1.png)
<!-- endtab -->
<!-- tab 完成代码 -->
```HTML
<body>
    <div id="container">
        <div class="center float">这是一段内容，显示中间，自适应，优先加载 </div>
        <div class="left float">this is left</div>
        <div class="right float">this is right</div>
    </div>
    <div class="footer">this is footer</div>
</body>
<style>
    /* 1.添加浮动 */
    .float {
        float: left;
    }
    /* 2.设置内边距空出左右的内容的宽度 */
    #container {
        padding-left: 300px;
        padding-right: 300px;
    }
    .center {
        background-color: #ffa;
        width: 100%;
    }
    .left {
        width: 300px;
        background-color: #faf;
        /* 左移动父元素100%宽度+自己的宽度 */
        margin-left: calc(-100% - 300px)
    }
    .right {
        width: 300px;
        background-color: #afa;
        /* 使自己的宽度为0，可以浮动到想要的位置 */
        margin-right: -300px;
    }
    .footer {
        background-color: #aaa;
        /* 清楚浮动的高度坍塌 */
        clear: both;    
    }
</style>
```
<!-- endtab -->
<!-- tab 最后渲染效果 -->
![](/image/textImg/float-2.png)
<!-- endtab -->
<!-- tab 总结 -->
1. 使用float布局
2. 利用padding使父容器空出左右两边的距离
3. 利用margin-left移动自身位置，利用margin-right缩小自身宽度
4. 清除浮动
<!-- endtab -->
{% endtabs %}

## 3.清除浮动的方式
> 1. 利用BFC清除浮动，即在父元素添加overflow:hidden
  2. 在浮动元素后添加空的块级元素，写上clear:both,常用伪类写法
    ``` CSS
    .clearfix::after {
        content: '';
        display: block;
        clear: both;
    }
    .clearfix {
        zoom: 1;   /* 兼容IE低版本 */
    }
    ```



