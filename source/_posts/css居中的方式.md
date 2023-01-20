---
title: Css实现居中的方式
date: {{ date }}
tags: 
 - css
 - 前端
categories: 
 - css
cover: /image/cover9.jpg
---
## 1. 水平居中
> margin: 0 auto;
-- 作用在<font color="red">子元素</font>上
-- 只能居中<font color="red">块级元素</font>
> text-align: center;
-- 作用在<font color="red">父级元素</font>上
-- 只能居中<font color="red">行内元素和行内块元素</font>

## 2. 水平+垂直居中
### 2.1 绝对定位居中
> 思路：
-- 利用绝对定位的方式，设置left:50%, top:50% , 再各自向左/上移动自身宽高的一半<脱离文档流>

#### 2.1.1 绝对定位+margin
{% tabs 绝对定位+margin %}
<!-- tab 绝对定位+margin -->
做法：
-- 设置margin-top/margin-left为负的宽高的一半
局限：
-- 必须知道自身的宽高
<!-- endtab -->

<!-- tab 代码演示 -->
```HTML
<style>
   .father{
       height: 500px;
       background:#faf; 
       position: relative;
   }
   .son{
       width: 100px;
       height: 150px;
       background: #aff;
       position: absolute;
       top: 50%;
       left: 50%;
       margin-top: -75px;
       margin-left: -50px;
   }
</style>
<body>
    <div class="father">
        <div class="son"></div>
    </div>
</body>

```
<!-- endtab -->
{% endtabs %}

#### 2.1.2 绝对定位+calc
{% tabs 绝对定位+calc %}
<!-- tab 绝对定位+calc -->
做法：
-- 设置left/top: calc(50% - 自身宽高的一半)
局限：
-- 必须知道自身的宽高
<!-- endtab -->
<!-- tab 代码演示 -->
```HTML
<style>
   .father{
       height: 500px;
       background:#faf; 
       position: relative;
   }
   .son{
       width: 100px;
       height: 150px;
       background: #aff;
       position: absolute;
       top: calc(50% - 75px);
       left: calc(50% - 50px);
   }
</style>
<body>
    <div class="father">
        <div class="son"></div>
    </div>
</body>

```
<!-- endtab -->
{% endtabs %}

#### 2.1.3 绝对定位+transform
{% tabs 绝对定位+transform %}
<!-- tab 绝对定位+transform -->
做法：
-- 设置transform: translate(-50%,-50%)
优势(常用)：
-- 不需要知道自身的宽高
<!-- endtab -->
<!-- tab 代码演示 -->
```HTML
<style>
  .father{
       height: 500px;
       background:#faf; 
       position: relative;
      }
  .son{
       width: 100px;
       height: 150px;
       position: absolute;
       top: 50%;
       left: 50%;
       transform: translate(-50%,-50%);
}
</style>
<body>
    <div class="father">
        <div class="son"></div>
    </div>
</body>

```
<!-- endtab -->
{% endtabs %}

#### 2.1.4 tabs 绝对定位+margin:auto
{% tabs 绝对定位+margin:auto %}
<!-- tab 绝对定位+calc -->
做法：
-- 设置绝对定位(left/right/top/bottom均为0)
-- 设置margin: auto
优势：
-- 不需要知道自身的宽高
<!-- endtab -->
<!-- tab 代码演示 -->
```HTML
<style>
  .father{
       height: 500px;
       background:#faf 
       position: relative;
      }
  .son{
       width: 100px;
       height: 150px;
       position: absolute;
       top: 0;
       left: 0;
       bottom: 0;
       right: 0;
       margin: auto;
}
</style>
<body>
    <div class="father">
        <div class="son"></div>
    </div>
</body>

```
<!-- endtab -->
{% endtabs %}

### 2.2 弹性布局
{% tabs 弹性布局 %}
<!-- tab 做法 -->
-- 设置display: flex
-- 设置主轴和交叉轴对齐方式为center
<!-- endtab -->
<!-- tab 代码演示 -->
```HTML
<style>
.father{
        height: 500px;
        background:#faf; 
        display: flex;
        justify-content: center;
        align-items:center;
    }
.son{
    width: 100px;
    height: 150px;
    }
</style>
```
<!-- endtab -->
{% endtabs %}

### 2.3 inline元素
> 做法
-- 设置line-height的值等于height的值
-- 设置text-align: center

