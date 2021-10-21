# SASS入门

## 1.变量引用

```scss
/* 定义变量与值 */
$bgcolor: lightblue;
$textcolor: darkblue;
$fontsize: 18px;

/* 使用变量 */
body {
  background-color: $bgcolor;
  color: $textcolor;
  font-size: $fontsize;
}
```

使用sass进行操作可以将变量进行无限引用：

通过sass进行编译操作：

sass 01-sass.scss 01-sass.css 来得到一个css文件：

得到了一个与之对应的css文件：

```css
@charset "UTF-8";
/* 定义变量与值 */
/* 使用变量 */
body {
  background-color: lightblue;
  color: darkblue;
  font-size: 18px;
}
```

>   变量名使用下划线进行操作

