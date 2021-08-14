# zotero 与 obsidian 配合提高阅读效率 - 知乎
## **zotero 与 obsidian 配合提高阅读效率**

[Zotero](https://link.zhihu.com/?target=https%3A//www.zotero.org/)是一款不错的文献管理工具，开源，免费，功能强大。作为医学生，我们可以用它来管理文献。

[Obsidian](https://link.zhihu.com/?target=https%3A//obsidian.md/)是一款强大的笔记软件，开源，支持双向链接，有利于我们进行知识循环。

如何把二者联合起来，以提高我们的阅读效率，今天我们来探讨一下这个话题。

## **我是如何做的？**

假如我从[PubMed](https://link.zhihu.com/?target=https%3A//pubmed.ncbi.nlm.nih.gov/) 或[中国知网](https://link.zhihu.com/?target=http%3A//xn--fiqs8scw2afhg/)下载了一些关于关键词 “胸腺瘤” 的文献，并存在了我的电脑桌面上，准备进行阅读。没错，就是下面这篇。

![](https://pic4.zhimg.com/v2-673baa13b5019e85327185dc72215f2b_b.png)

现在这篇文献的 PDF 格式文件我已经下载好了，我首先要做的是把这个文件导入到 zotero 中。我可以直接把这个文件拖动到 zotero 中，然后就是这个样子：

![](https://pic1.zhimg.com/v2-3a002653be9569cf801460f151e5a074_b.jpg)

在这里，如果是英文文献，我可选中这篇文献后，点击鼠标右键，点击重新抓取 pdf 的元数据，然后这篇文章的元数据就有了。

![](https://pic1.zhimg.com/v2-813181fdd7436c9c6e4e473825501e30_b.jpg)

### **“坑一”- 中国知网文献无法获取元数据怎么办？**

从知网下载的中文文献的元数据获取不了。解决办法是安装 “[jasminum: 茉莉花](https://link.zhihu.com/?target=https%3A//github.com/l0o0/jasminum)插件。

> 具体安装方法可参考：  

[简单的 Zotero CNKI 中文插件 - 知乎 (知乎 - 有问题，就会有答案)](https://link.zhihu.com/?target=http%3A//xn--%3Azotero%2520cnki%2520-ch60ae3i633adf3fx3hokmgk4gchhykz/)

安装好茉莉花插件之后，我们选中这篇文献，点击鼠标右键，点击抓取知网元数据。

![](https://pic1.zhimg.com/v2-beac2ab4016e377f506919b46699c0c4_b.jpg)

然后这篇文章的索引信息就有了。

![](https://pic2.zhimg.com/v2-69ce2e93d85a5c0d3e157d12cb2ee289_b.png)

### **开始文献阅读**

在 zotero 中双击我刚才要阅读的那篇文献的 pdf，它就会在默认的 PDF 阅读器中打开，这里我用的是”Adobe Acrobat Pro DC”，我会一边阅读，一边做一些标注。比如这样：

![](https://pic1.zhimg.com/v2-548b365716a78e34f6b64326e6f25028_b.jpg)

阅读完这篇文献之后，我会把 pdf 阅读器关掉，关掉之前会有弹出提示：要不要保存？这里一点不要忘了保存。

### **提取文献内容到 Obsidian**

### **准备工作 1：zotero 安装插件**

1.  [ZotFile](https://link.zhihu.com/?target=http%3A//zotfile.com/)插件
2.  [Mdnotes for Zotero](https://link.zhihu.com/?target=https%3A//github.com/argenos/zotero-mdnotes)插件
3.  [Better BibTex for ZoteroI](https://link.zhihu.com/?target=https%3A//retorque.re/zotero-better-bibtex/installation/)插件

安装方法比较简单，先下载，然后常规安装，这里就不赘述了。安装完成后是这个样子：

![](https://pic4.zhimg.com/v2-6b8247cee8b729003ccd72476ccd2953_b.jpg)

### **准备工作 2：Obsidian 安装插件**

1.  [obsidian-citation-plugin](https://link.zhihu.com/?target=https%3A//github.com/hans/obsidian-citation-plugin/releases/tag/0.4.3)插件

安装方法比较简单，先下载，然后常规安装，这里就不赘述了。安装完成后是这个样子：

![](https://pic3.zhimg.com/v2-e4205099e438bb5effe2e57eeb0b338e_b.jpg)

> 安装完成后需要进行一些设置，具体设置方法参考：  

[zotero 插件\_ob 和 zotero 连用（Citation 插件介绍）](https://link.zhihu.com/?target=http%3A//zotero%25E6%258F%2592%25E4%25BB%25B6_ob%25E5%2592%258Czotero%25E8%25BF%259E%25E7%2594%25A8%25EF%25BC%2588citation%25E6%258F%2592%25E4%25BB%25B6%25E4%25BB%258B%25E7%25BB%258D%25EF%25BC%2589_weixin_39758618%25E7%259A%2584%25E5%258D%259A%25E5%25AE%25A2-csdn%25E5%258D%259A%25E5%25AE%25A2/)

### **zotero 中 pdf 注释提取**

我们回到 zotero，选中我刚才阅读完的那篇文献，点击右键，在弹出的选项中找到 “Manage Attachments”，然后进一步选择 “Extract Annotations”。

![](https://pic4.zhimg.com/v2-f4b90897063ed6c4dbe24d591e8ac1cf_b.jpg)

然后就会提取出 pdf 注释，并生成一个新文件如下：

![](https://pic4.zhimg.com/v2-88d1aeb4a20886397108a7fe3dc30b53_b.jpg)

我们可以点击这个文件看一下，可以看到最右侧就显示了我们之前在 pdf 阅读器中注释的内容。

### **从 zotero 提取文献信息到 obsidian**

下面开始提取我们刚才阅读的那篇文献的信息到 obsidian，我们选中文献，点击右键，在弹出的选项中找到 “Mdnotes”，然后进一步选择 “Create Mdnotes file”。（这里有三个选择，另外两个 “Export to markdown”，“Batch export to markdown”，你可以自己尝试一下，看看会有什么效果。）

![](https://pic4.zhimg.com/v2-778ebc32ea8bad655f908351e15dc9f3_b.jpg)

我们把 “Create Mdnotes file” 建立的文件存储在一个文件夹中，这里我已经在 obsidian 文件夹的根目录建立一个文件夹，并命名文件夹名为“6 - References”。我就把新建立的 markdown 文件存在这里。

![](https://pic2.zhimg.com/v2-e3e7814faa5cf270d7263030db4aca29_b.jpg)

点击” 打开”，然后点击 “保存”，然后我们在 obsidian 中来看一下新保存的这个文件。

![](https://pic2.zhimg.com/v2-a5003f51fee4f44407d861a5c497a655_b.jpg)

这个样子好像并不符合我可胃口，需要调整一下这个内容模板。

### **调整保存到 obsidian 中的内容模板**

我们希望从 zotero 提取到 obsidian 中的内容符合我们的阅读习惯，我们就要制作自己的模板。首先，在菜单栏选择” 工具 “, 然后选择”Mdnotes preferences“。

![](https://pic1.zhimg.com/v2-b335aefb33086b37d5498b8d6812ca68_b.jpg)

点击之后，会弹出一个对话框，如下：

![](https://pic2.zhimg.com/v2-c403715f86931703e9705b7d921bfa8d_b.jpg)

我们在”Template folder“中选择” 模板所在的文件夹 “，我这里还是选择文件夹 ”6 - References“。因为，在这之前我已经把我做好的模板放在了这个文件夹中。

![](https://pic4.zhimg.com/v2-aa9ee257d74797863bb623b605cb7c0b_b.jpg)

这个模板也是一个 markdown 文件，我的这个模板的名称是”Mdnotes Default Template“样式是这样的：

![](https://pic2.zhimg.com/v2-4be1b93816f128fab3eb746598b647e1_b.jpg)

### **再次从 zotero 提取文献信息到 obsidian**

设置好模板之后，我们把原先那个从 zotero 导入到 obsidian 的文件删掉，然后重新导入一次，看看会有什么变化。操作方法同前。

![](https://pic4.zhimg.com/v2-778ebc32ea8bad655f908351e15dc9f3_b.jpg)

然后我们看看应用了” 模板 “后，会是什么样子：

![](https://pic2.zhimg.com/v2-1c0da0598e7d3adbc5fba5cba2c628a1_b.jpg)

是不是与没有应用模板的时候发生了很大的变化，我们看看这里都包含了哪些信息：

1.  标题：

![](https://pic1.zhimg.com/v2-f27511bfb0678b2acf0e8d6e96802fb8_b.png)

1.  作者：

![](https://pic2.zhimg.com/v2-f4c0dac6c474421cce4874e423d52789_b.png)

2. 发表时间：

![](https://pic4.zhimg.com/v2-fabb23e29ac79bdec843d16219daf0eb_b.png)

3. 标签：

![](https://pic1.zhimg.com/v2-b9477ab43317fab4e1ad97e13f4f3b18_b.png)

4. 文献的网页链接：

![](https://pic2.zhimg.com/v2-be4898c47fa73f0eb8e131cacded5e95_b.png)

5. 连接到 zotero 所在位置

![](https://pic1.zhimg.com/v2-e91eaac4de7b6f7db09549d391e50e98_b.png)

6. 本地 PDF 附件

![](https://pic1.zhimg.com/v2-76eb77a004a751498fb5776a0c445b8c_b.png)

7. pdf 中的注释内容

![](https://pic4.zhimg.com/v2-810cc2677a368d2d3135742906e7ff63_b.jpg)

好吧，就是这些内容，如果你还需要文献的其他信息，你可以自己编辑自己的模板。

## **小结**

本文读起来可能有些复杂，主要是设置过程，安装许多插件，但一旦设置完成，用起来就会很方便。特别是 obsidian 强大的标签和链接功能，我们很容易找到我们之前阅读过的文献内容，而不至于读完某篇文献后，想用的时候就再也找不到了，然后就，没有然后了。所以，不要害怕这个设置过程，一步一步做下来，你会感觉也不是很复杂的。

如果按照这个教程没有设置成功的化，记得告诉我。我已经尽力写的明白，但能力有限，有些时候自己可能表达的不太清楚，如果不清楚的化可以给我留言，共同探讨解决。我的个人公众号及 B 站 up 都名为：阿狸的 Blog，欢迎点赞关注。然后我会做一个同名的视频教程，可能会讲得清楚一些。好吧，我是阿狸，再见。 
 [https://zhuanlan.zhihu.com/p/389205793](https://zhuanlan.zhihu.com/p/389205793)
