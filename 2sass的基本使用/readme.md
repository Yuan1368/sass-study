# sass 的基本使用

## 普通变量与默认变量

我们在之前的内容里提到过，我们使用`$`符号来声明 sass 中的变量，后设置的变量值会覆盖前设置的变量值，例：

```scss
//demo1.scss
$boxWidth:100px;
$boxWidth:200px;
div{
  width: $boxWidth;
}
```

编译结果：

```css
div {
  width: 200px;
}
/*# sourceMappingURL=demo1.css.map */
```

sass 允许使用`@import`导入外部 sass 样式，例：

```scss
@import "demo1.scss"
div{
  width:$boxWidth;
}
```

编译结果：

```css
div {
  width: 200px;
}
/*# sourceMappingURL=demo2.css.map */
```

在声明的变量后放置`!default`表示这是个默认值，如果在默认值之前声明过该变量，并且在样式中使用了该变量，使用的是默认变量前的变量，默认变量不会覆盖之前声明过的变量。如果在默认值之后又声明了该变量，并且在样式中使用了该变量，使用的是默认变量后重新声明的变量，后声明的变量会覆盖前声明的变量的规则依然在。

```scss
//demo3.scss
$boxHeight: 100px;
$boxHeight: 200px !default;
div{
  height: $boxHeight;
}
```

编译结果：

```css
div {
  height: 100px;
}
/*# sourceMappingURL=demo3.css.map */
```

如果我们使用`@import`导入外部样式，如果存在两个相同的变量，那么`@import`的位置也会决定它是否会出现覆盖的现象。例：

```scss
//demo4.scss
$boxHeight: 300px;
@import "demo3.scss"
div{
  height: $boxHeight;
}
```

编译结果：

```css
div {
  height: 100px;
}
/*# sourceMappingURL=demo4.css.map */
```

`demo4.scss`中声明的`$boxHeight:300px`被`demo3.scss`中的`$boxHeight:100px;`覆盖了。

## 全局变量与局部变量

sass 在3.4版本之后已经可以正确地处理全局变量与局部变量了。所谓全局变量就是在 css 选择器外声明的变量，而局部变量是在选择器内部声明的，只在这个选择器的内部以及这个选择器下的子元素的局部范围内发挥作用。例：

```scss
$color : red;

body{
  color: $color;
  div{
    $color: blue;
    color: $color;
    span{
      color: $color;
    }
  }
  h1{
    color: $color;
  }
}
```

编译结果：

```css
body {
  color: red;
}

body div {
  color: blue;
}

body div span {
  color: blue;
}

body h1 {
  color: red;
}
/*# sourceMappingURL=demo5.css.map */
```

## 选择器嵌套

我们在 sass 那一章节里就已经知道了 sass 是可以嵌套选择器的，除了选择器嵌套外还可以嵌套属性与伪类。

### 属性嵌套

我们在 css 中习惯使用为元素设置的`id`属性及`class`属性来声明属性样式， scss 语法也允许使用`id`和`class`属性。例：

```scss
$bgColor: yellow;
.box{
  background-color: $bgColor;
  #box1{
    background-color: $bgColor;
  }
}
```

编译结果：

```css
.box {
  background-color: yellow;
}

.box #box1 {
  background-color: yellow;
}
/*# sourceMappingURL=demo6.css.map */
```

### 伪类嵌套

伪类嵌套与属性嵌套相似，但是需要与`&`符号配套使用。例：

```scss
.box{
  &::before,
  &::after{
    content:"";
    display: block;
  }
  &::before{
    color: blue;
  }
}
```

编译结果：

```css
.box::before, .box::after {
  content: "";
  display: block;
}

.box::before {
  color: blue;
}
/*# sourceMappingURL=demo7.css.map */
```

## 混合宏

在 sass 中我们可以使用混合宏来调用大量重复的内容，混合宏也有着类似于函数（甚至面向对象）的效果。

### 声明混合宏

在 sass 可以使用`@maxin`来声明混合宏，混合宏可以不带参数，也可以是带参数的混合宏。例：

```scss
//不带参数的混合宏声明
@mixin mainColor{
  color: blue;
  background-color: blue;
}

//带默认参数的混合宏声明
@mixin mainHeight($length:100px){
  height: $length;
  margin-top: $length;
}
```

### 调用混合宏

这些声明好的混合宏可以使用`@include`在需要的样式类进行调用。例：

```scss
//带参数的混合宏声明
@mixin mainHeight($length:100px){
  height: $length;
  margin-top: $length;
}

.box{
  @include mainHeight(200px);
}
```

编译结果：

```css
.box {
  height: 200px;
  margin-top: 200px;
}
/*# sourceMappingURL=demo8.css.map */
```

上述代码在调用`mainHeight`时，又给他赋了一个新值，新值会覆盖该宏声明时的默认值。这种调用就像是调用一个外部已经定义好的函数一样。

混合宏允许传递多个参数，这样就更像是一个函数的调用了。例：

```scss
@mixin length($height, $width) {
  height: $height;
  width: $width;
}

.box1{
  @include length(100px, 200px);
}
```

编译结果：

```css
.box1 {
  height: 100px;
  width: 200px;
}
/*# sourceMappingURL=demo8.css.map */
```

我们在使用 css 时会遇上需要使用多个属性值的情况，混合宏允许多个值使用`...`进行压缩。例：

```scss
@mixin boxShadow($boxshadow...) {
  box-shadow: $boxshadow;
}

div{
  @include boxShadow(0 1px 2px #ccc, 1px 0 2px rgb(0,230,222));
}
```

编译结果：

```css
div {
  box-shadow: 0 1px 2px #ccc, 1px 0 2px #00e6de;
}
/*# sourceMappingURL=demo9.css.map */
```

## 继承

我们可以使用`@extend`来继承已经存在的代码块。例：

```scss
@mixin mainColor($color) {
  color: $color;
  background-color: $color;
}

.box{
  width: 100px;
  height: 100px;
  @include mainColor(red);
}

.box1{
  @extend .box;
}
```

编译结果：

```css
.box, .box1 {
  width: 100px;
  height: 100px;
  color: red;
  background-color: red;
}
/*# sourceMappingURL=demo10.css.map */
```

### 占位符


