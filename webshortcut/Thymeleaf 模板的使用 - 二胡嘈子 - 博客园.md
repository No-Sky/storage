# Thymeleaf 模板的使用 - 二胡嘈子 - 博客园
**Thymeleaf**是现代化服务器端的 Java 模板引擎，不同与 JSP 和 FreeMarker，Thymeleaf 的语法更加接近 HTML，并且也有不错的扩展性。详细资料可以浏览[官网](http://www.thymeleaf.org/)。本文主要介绍 Thymeleaf 模板的使用说明。

### 模板（template fragments）###

**定义和引用模板**

日常开发中，我们经常会将导航栏，页尾，菜单等部分提取成模板供其它页面使用。

在 Thymeleaf 中，我们可以使用`th:fragment`属性来定义一个模板。

我们可以新建一个简单的页尾模板，如：/WEB-INF/templates/footer.html，内容如下：

```null
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
  <body>
    <div th:fragment="copyright">
      © 2016 xxx
    </div>
  </body>
</html> 
```

上面定义了一个叫做`copyright`的片段，接着我们可以使用`th:include`或者`th:replace`属性来使用它：

```null
<body>
  ...
  <div th:include="footer :: copyright"></div> 
</body>
```

其中 th:include 中的参数格式为 templatename::\[domselector],  
其中 templatename 是模板名（如 footer），domselector 是可选的 dom 选择器。如果只写 templatename，不写 domselector，则会加载整个模板。

当然，这里我们也可以写表达式：

```null
<div th:include="footer :: (${user.isAdmin}? #{footer.admin} : #{footer.normaluser})"></div>
```

模板片段可以被包含在任意`th:*`属性中，并且可以使用目标页面中的上下文变量。

**不通过 th:fragment 引用模板**

通过强大的 dom 选择器，我们可以在不添加任何 Thymeleaf 属性的情况下定义模板：

```null
...
<div id="copy-section">
 &copy; xxxxxx
</div>
...
```

通过 dom 选择器来加载模板，如 id 为 copy-section

```null
<body>
 ...
 <div th:include="footer :: #copy-section">
</div>
 </body>
```

**th:include 和 th:replace 区别**

th:include 是加载模板的内容，而 th:replace 则会替换当前标签为模板中的标签

例如如下模板：

```null
<footer th:fragment="copy"> 
&copy; 2016
</footer>
```

我们通过 th:include 和 th:replace 来加载模板

```null
<body> 
  <div th:include="footer :: copy"></div>
  <div th:replace="footer :: copy"></div>
 </body>
```

返回的 HTML 如下：

```null
<body> 
   <div> &copy; 2016 </div> 
  <footer>&copy; 2016 </footer> 
</body>
```

### 模板参数配置 ###

th:fragment 定义模板的时候可以定义参数：

```null
<div th:fragment="frag (onevar,twovar)"> 
  <p th:text="${onevar} + ' - ' + ${twovar}">...</p>
</div>
```

在 th:include 和 th:replace 中我们可以这样传参：

```null
<div th:include="::frag (${value1},${value2})">...</div>
<div th:include="::frag (onevar=${value1},twovar=${value2})">...</div>
```

此外，定义模板的时候签名也可以不包括参数：`<div th:fragment="frag">`，我们任然可以通过`<div th:include="::frag (onevar=${value1},twovar=${value2})">...</div>`这种方式调用模板。这其实和`<div th:include="::frag" th:with="onevar=${value1},twovar=${value2}">`起到一样的效果

**th:assert 断言**

我们可以通过 th:assert 来方便的验证模板参数`<header th:fragment="contentheader(title)" th:assert="${!#strings.isEmpty(title)}">...</header>`

### th:remove 删除代码 ###

假设我们有一个产品列表模板：

```null
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    <td>
      <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
      <a href="comments.html" 
         th:href="@{/product/comments(prodId=${prod.id})}" 
         th:unless="${#lists.isEmpty(prod.comments)}">view</a>
    </td>
  </tr>
</table>
```

这时一个很常规的模板，但是，当我们直接在浏览器里面打开它（不（不使用 Thymeleaf ），它实在不是一个很好的原型。因为它的表格中只有一行，而我们的原型需要更饱满的表格。

如果我们直接添加了多行，原型是没有问题了，但通过 Thymeleaf 输出的 HTML 又包含了这些事例行。

这时通过 th:remove 属性，可以帮我们解决这个两难的处境，

```null
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
    <th>COMMENTS</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    <td>
      <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
      <a href="comments.html" 
         th:href="@{/product/comments(prodId=${prod.id})}" 
         th:unless="${#lists.isEmpty(prod.comments)}">view</a>
    </td>
  </tr>
  <tr class="odd" th:remove="all">
    <td>Blue Lettuce</td>
    <td>9.55</td>
    <td>no</td>
    <td>
      <span>0</span> comment/s
    </td>
  </tr>
  <tr th:remove="all">
    <td>Mild Cinnamon</td>
    <td>1.99</td>
    <td>yes</td>
    <td>
      <span>3</span> comment/s
      <a href="comments.html">view</a>
    </td>
  </tr>
</table>
```

其中 th:remove 的参数有如下几种：

-   all 删除当前标签和其内容和子标签
-   body 不删除当前标签，但是删除其内容和子标签
-   tag 删除当前标签，但不删除子标签
-   all-but-first 删除除第一个子标签外的其他子标签
-   none 啥也不干

当然，我们也可以通过表达式来传参，  
`<a href="/something" th:remove="${condition}? tag : none">Link text not to be removed</a>`

* * *

以上为 Thymeleaf 中模板的一些用法，各位看官请点赞。 
 [https://www.cnblogs.com/lazio10000/p/5603955.html](https://www.cnblogs.com/lazio10000/p/5603955.html)
