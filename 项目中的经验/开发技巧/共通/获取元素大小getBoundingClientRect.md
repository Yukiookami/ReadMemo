# 获取元素大小 getBoundingClientRect

## 定义

`getBoundingClientRect` 方法返回元素的大小及其相对于视口的位置。这是一个 HTML5 新增的方法。

## 语法

```js
element.getBoundingClientRect();
```

## 返回值

返回一个 `DOMRect` 对象，包含元素的位置和大小信息。

## 示例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      #box {
        width: 100px;
        height: 100px;
        background-color: red;
      }
    </style>
  </head>

  <body>
    <div id="box"></div>
    <script>
      const box = document.getElementById("box");
      const rect = box.getBoundingClientRect();
      console.log(rect); // DOMRect { x: 8, y: 8, width: 100, height: 100, top: 8, right: 108, bottom: 108, left: 8 }
    </script>
  </body>
</html>
```

## 实际应用

- 精准获取元素大小
- 判断元素是否在视口内，可用于懒加载等场景
- 实现元素的拖拽和动画效果
- 根据元素位置动态调整 UI 元素
