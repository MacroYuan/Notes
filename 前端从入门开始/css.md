css描述的是网页各元素的样式，一个元素怎样显示给用户，在页面中如何布局，动态展示还是固定位置大小，都可以通过css来实现。

一条CSS规则有两部分组成：选择器和样式规则。如：

```css
button {
    color: tomato;
    font-size: 200%;
}
```

在网页中通过`<link>`链接到css文件

```html
<link rel="stylesheet" type="text/css" href="./xx.css">
```

<link>有三个属性：rel、type、href：

- `rel="stylesheet"`：说明此资源与页面的关系是样式表。
- `type="text/css"`：说明此资源的文件格式是text/css
- `href="./xx.css"`：说明了此资源的位置

而在css文件中可以直接通过`@import`引入外部css文件。

```css
@import url(./style.css)
```

### 选择器

样式表中主要有两类选择器来选择特定的元素：**类选择器.class**和**ID选择器#id**

 类选择器

- HTML中class属性的顺序不重要。这意味着`class="a b"`和`class="b a"`等价
- 在不同浏览器模式下，类名的大小写敏感也是不同的。这意味着`.a`不一定会选中`class="A"`，所以应保证类选择器和类名的大小写是完全一致的。



ID选择器

- ID选择器用于精确选择。一个HTML页面不允许出现相同的ID。
- 一个元素只能拥有一个ID。
- ID选择器的权重比元素选择器和类选择器高很多。这意味着如果几种选择器下的样式出现冲突，则浏览器将作用ID选择器下的样式。
- 在不同的浏览器模式下，ID的大小写敏感也是不同的。



属性选择器`[prop=value]`

根据元素所包含的属性值来选择元素，从CSS2开始支持属性选择器。

*=判断属性值是否包含某些字符

^=可以判断所有属性值以某段字符开始的元素

$=可以判断所有属性值以某段字符结束的元素

~=可以判断是否包含特定的属性值，该属性值需要被空格分割

|=与*=类似，但只在意被-分割的属性值，且只选中第一项



全局选择器

*表示选择全部元素



#### 分组选择器

分组选择

多条件选择

后代选择

子选择

相邻兄弟选择

通用兄弟选择



#### 伪类——按元素状态指定样式

伪类选择的是元素的状态与外界的关系。例如当光标悬停在元素上时改变样式：

```css
a:hover{ /* 当光标悬停在<a>上时作用以下样式 */
    border: 2px solid #000;
}
```

- :active  激活状态
- :focus  聚焦状态
- :checked  勾选状态
- :disabled  禁用状态
- :enabled  可用状态
- :empty  空值
- :first-child  老大，选中一组平行元素中最先出现的元素
- :last-child  老幺
- :nth-child(n)  排行，选中一组平行元素中排在n位的元素
- :nth-last-child(n)  倒数排行
- :first-of-type  同类中的老大
- :last-of-type  同类中的老幺
- :nth-of-type(n)  同类排行
- :nth-last-of-type(n)  倒数同类排行
- :not()  排除
- :only-child  独苗，用于选择没有兄弟的元素，即父级元素的唯一子元素



- ::before  前缀元素
- ::after  后缀元素
- ::first-line  首行
- ::first-letter  首字，块级元素的首字母
- ::placeholder  空白占位符
- ::selection  选择范围



## CSS盒模型

内容区（content area）、内边距（padding）、外边距（margin）、边框（border）。

![](../image/css1.gif)







### 元素定位

1. 静态static
2. 相对定位relative
3. 绝对定位absolute
4. 固定定位fixed——与窗口同步
5. 黏滞定位sticky



### 浮动

#### 单元素浮动

```css
a {
    float: left;
}
```

浮动后，其padding、border、margin在各个方向都对周围产生影响。

#### 多元素浮动

默认情况下，浮动元素的位置不会超过其容器的内容区。

默认多个元素不会相互重叠的。当多个并列的元素同时浮动时，它们会想文字一样流动
当浮动的元素更多以至于一行放不下时，元素会自动换行。



### 响应式布局

一套样式常常不能适配PC、手机等各种不同设备，这时需要根据设备类型展示响应的样式。

媒介查询语法：

```css
@media 条件 {
    满足条件才会作用的样式规则
}
```

条件有：

- all：所有媒介
- screen：显示屏。如电脑、手机、电子阅读器
- print：打印显示

```css
/* 如果视窗小于450px，则加载大括号里的样式 */
@media (max-width: 450px) {
    p {
        font-size: 5px;
    }
}
```

