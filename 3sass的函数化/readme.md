# sass 函数化

我们在之前的内容中了解到， sass 的运用使得 css 更符合计算机语言的一些特性，例如它拥有变量，混合宏的使用使开发者可以像运用函数一样来写 css 样式，这节内容我们将了解更多 sass 的知识，这些内容将使 sass 更加具有其它计算机语言的函数特性,

## sass的数值类型

sass 也是具有数值类型的，这些数值类型既有与其它计算机语言相似的地方，也有不同的地方。主要有以下这些数值类型：

* 数字，1、2...还包括10px这种的。
* 字符串，有引号字符串或者无引号字符串，例如"bar"、'foo'、!important...
* 颜色，比如说 blue、 #ccc、rab(255,255,0)。
* 布尔型，true和false。
* 空值，null
* 值列表，使用空格或者逗号分隔开的都算，如1.5em 1em 0 2em 、 Helvetica, Arial, sans-serif。
  
需要注意的是，在使用`#{}`插值语法后，有引号字符串会被编译成无引号字符串。例：

```scss
$selector:".header";
div.box #{$selector}{
  width: 100px;
  color: red;
  &::after{
    content: "";
  }
}
```

编译结果：

```css
div.box .header {
  width: 100px;
  color: red;
}

div.box .header::after {
  content: "";
}
/*# sourceMappingURL=demo1.css.map */
```

## 数值计算

sass 可以进行基本的数值运算，例：

```scss
div{
  width: 100px+200px;
  height: 100px-50px;
  margin-top: 100px*2;
  margin-bottom: (100px / 2);
  margin-left: 100px%44;
}
```

编译结果：

```css
div {
  width: 300px;
  height: 50px;
  margin-top: 200px;
  margin-bottom: 50px;
  margin-left: 12px;
}
/*# sourceMappingURL=demo2.css.map */
```

除了除法运算时，如果不加括号，除法运算符会被当成原始的斜杠`/`外，其它的运算符正常使用即可。

需要注意的是，如果运算的两个数值单位不一致会报错。

```scss
div{
  margin-right: 100px - 1rem;
}
```

![运行结果](./img/1.png)

另外如果在除法运算时，使用了两个带符号的变量，编译时会消除掉符号。

### 颜色值运算

颜色值可以被当成普通数值进行加减乘除上的计算，例：

```scss
div{
  color: #013126 + #013000;
  color: #123000 * 2;
  color: #cccccc - #c00000;
  color: (#cccccc / 2);
}
```

编译结果：

```css
div {
  color: #026126;
  color: #246000;
  color: #0ccccc;
  color: #666666;
}
/*# sourceMappingURL=demo4.css.map */
```

## 变量运算

除了数值运算外，sass 也允许我们使用变量进行运算，例：

```scss
$base-width: 200px;
$length: 100px;

.header{
  width: $base-width - $length;
}
```

编译结果：

```css
.header {
  width: 100px;
}
/*# sourceMappingURL=demo3.css.map */
```

需要注意的是，需要注意代码的规范，运算符需要与变量名至少保持一个空格以上的距离，在这里如果不规范会把减号当成连接符`-`。

## sass 的控制命令

### @if

对其它计算机语言而言，`if...else...`是比较常见的条件控制语句，sass 的`@if`也具有相同的作用，并且可以与`@else`和`@else if`配合使用。

```scss
@mixin easySheet($length) {
  @if($length > 100px){
    width: $length;
    color: red;
  }
  @else if($length > 50px){
    width: 75px;
    color: yellow;
  }
  @else{
    width: 50px;
    color: blue;
  }
}

.header{
  @include easySheet(200px);
  .box{
    @include easySheet(60px);
    .box1{
      @include easySheet(20px);
    }
  }
}
```

编译结果：

```css
.header {
  width: 200px;
  color: red;
}

.header .box {
  width: 75px;
  color: yellow;
}

.header .box .box1 {
  width: 50px;
  color: blue;
}
/*# sourceMappingURL=demo5.css.map */
```

### @for循环

sass 可以使用`@for`循环，主要有以下两种用法：

```scss
@for $i from <start> through <end>
@for $i from <start> to <end>
```

`$i`是变量，`start`表示开始值，`end`表示结束值，`through`与`to`的区别在于`through`用法包含结束值，而`to`不包含结束值。

用法示例：

```scss
@for $i from 1 through 5{
  .box#{$i}{
    width: 10px * $i;
  }
}
```

编译结果：

```css
.box1 {
  width: 10px;
}

.box2 {
  width: 20px;
}

.box3 {
  width: 30px;
}

.box4 {
  width: 40px;
}

.box5 {
  width: 50px;
}
/*# sourceMappingURL=demo6.css.map */
```

### @while循环

`@while`循环与`@for`循环用法类似，只需要`@while`后的表达式为`true`就可以进行循环，例：

```scss
$length: 10 !default;
@while $length > 0{
  .box#{$length}{
    width: $length * 10px;
  }
  $length : $length - 1;
}
```

编译结果：

```css
.box10 {
  width: 100px;
}

.box9 {
  width: 90px;
}

.box8 {
  width: 80px;
}

.box7 {
  width: 70px;
}

.box6 {
  width: 60px;
}

.box5 {
  width: 50px;
}

.box4 {
  width: 40px;
}

.box3 {
  width: 30px;
}

.box2 {
  width: 20px;
}

.box1 {
  width: 10px;
}
/*# sourceMappingURL=demo7.css.map */
```

### @each

遍历一个列表变量，每次从列表变量中取出一个值，其用法类似于 ES6 的`for...of...`循环用法。语法格式为：

```scss
@each $var in <list>
```

`$var`是变量，`list`是列表变量。

用法示例：

```scss
$selectors: div h1 span;
@each $var in $selectors{
  #{$var}{
    color: red;
    width: 100px;
    height: 100px;
  }
}
```

编译结果：

```css
div {
  color: red;
  width: 100px;
  height: 100px;
}

h1 {
  color: red;
  width: 100px;
  height: 100px;
}

span {
  color: red;
  width: 100px;
  height: 100px;
}
/*# sourceMappingURL=demo8.css.map */
```

## 函数功能

scss 中内置了一些比较常用的函数，此节内容将会简单介绍。

### 字符串函数

#### unquote($string)

这个函数不会区分单双引号，主要用来删除字符串开头和结尾的单引号或者双引号，对于字符串中间的引号会保留开始到中间引号的部分，但是会删除中间引号之后的内容（由于它不区分单双引号，这算是个bug，不要乱用）；如果字符串不含有引号，则会报错。

用法示例：

```scss
.text1{
  content: unquote($string: "'Hello World!'");
}

.text2{
  content: unquote($string: '"Hello World!"');
}

.text3{
  content: unquote($string: "H'ello");
}
```

编译结果：

```css
.text1 {
  content: 'Hello World!';
}

.text2 {
  content: "Hello World!";
}

.text3 {
  content: H;
}
/*# sourceMappingURL=demo9.css.map */
```

#### quote函数

与`unquote`函数的作用相反，会给没有引号的内容添加上单双引号，如果本身含有单引号会被替换成双引号，中间的引号会被保留。例：

```scss
.box1{
  content: quote($string: "'Hello'");
}

.box2{
  content: quote($string: Hello);
}

.box3{
  content: quote($string: 'Hello');
}
```

编译结果：

```css
.box1 {
  content: "'Hello'";
}

.box2 {
  content: "Hello";
}

.box3 {
  content: "Hello";
}
/*# sourceMappingURL=demo10.css.map */
```

#### 