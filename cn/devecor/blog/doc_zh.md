# 于sphinx上实践文档象限

实践一种行之有效的文档组织方式

## 序

幸运的是，有这样一种确保正确生成结构化文档的模式：[文档象限](https://documentation.divio.com/)。

在一个坐标系下，划分象限的两条轴描述了文档所具有的属性，横轴描述文档的使用场景是偏于工作的还是偏于学习的，纵轴描述文档是偏于理论的还是偏于实践的。

![image](https://devecor.cn/image/ad0ee909-e5bc-492b-8485-4aaccf2511a4/image.png)

此处或许有必要明确引用一下文档四象限的内涵:

> 文档必须包涵并围绕四个不同的功能点进行结构化，他们就是： **tutorials, how-to guides, technical reference and explanation**
>
|        | Tutorials            | How-to guides              | Reference          | Explanation              |
| ------ | -------------------- | -------------------------- | ------------------ | ------------------------ |
| 面向   | 学习                 | 特定目标                   | 信息               | 理解                     |
| 必须   | 能够让新手从0开始    | 演示如何解决一个特定的问题 | 描述你的作品       | 解释原理                 |
| 形如   | 一个课程             | 一系列步骤                 | 枯燥的描述         | 描述性的解释说明         |
| 类似于 | 教一个小孩子如何做饭 | 一本烹饪书籍上的一条食谱   | 百科全书的参考文章 | 一篇关于烹饪社会史的文章 |

这会使你的文档看起来简单明了，对吧？

相比于直接开发一个网站，采用文档生成工具能让事情变得容易一些。`sphinx`是笔者见过的一款很不错的文档生成工具，该工具最初是用来创建`Python`官方文档的，同时也很适合许多其他语言的软件项目的文档化。

本文我们将探讨文档象限如何在`sphinx`这个工具上落地。

 ## 目录

- [于sphinx上实践文档象限](#于sphinx上实践文档象限)
  - [序](#序)
  - [目录](#目录)
  - [实践参考](#实践参考)
    - [浅接触sphinx](#浅接触sphinx)
    - [使用sphinx组织文档象限](#使用sphinx组织文档象限)
    - [部署你的sphinx文档](#部署你的sphinx文档)

## 实践参考

### 浅接触sphinx

`sphinx`作为Python的一个第三方lib发行给用户。所以你得安装一个python的解释器，笔者推荐使用[miniconda](https://docs.conda.io/en/latest/miniconda.html)管理你的python环境。当然，你尽可以选择你喜欢的方式。[^1]

安装完miniconda之后，这个命令`conda -V`能显示miniconda的版本号，例如`conda 4.12.0`

接下来为sphinx文档启用专用的python环境[^3]

```sh
conda create --name docs  python=3.10
conda activate docs
```

当你有了python解释器后，就能够安装sphinx及其扩展，保存下面的依赖到`requirements.txt`文件，便能使用`python -m pip install -r requirements.txt`安装它们。

```
sphinx >= 4.5.0
sphinxcontrib-mermaid >= 0.7.1
myst-parser >= 0.17.2
sphinx-book-theme >= 0.3.2
```

是时候初始化我们的文档了:

```sh
sphinx-quickstart docs
```
这会生成一个docs目录，[sphinx官方](https://www.sphinx-doc.org/en/master/tutorial/getting-started.html)已经给出了足够优秀的说明，本文不再赘述。

看到这里你可能会疑惑上文中`requirements.txt`多余三个依赖包是干什么用的，尽管它们都是可选的，却非常实用，让我一一解释：

* [myst-parser](https://myst-parser.readthedocs.io/en/latest/index.html) - 一个sphinx扩展工具，能让你在sphinx下使用markdown编写你的文档(sphinx默认的文档标记语法是RestructuredText)
* [sphinxcontrib-mermaid](https://myst-parser.readthedocs.io/en/latest/intro.html#extending-sphinx) - 支持`mermaid`绘图，配合myst-parser可以直接基于markdown进行绘图。例如：
  ````
  ```{mermaid}
  flowchart LR
    A --> B
  ```
  ````

  渲染效果：

  ```mermaid
  flowchart LR
    A --> B
  ```

* [sphinx-book-theme](https://sphinx-book-theme.readthedocs.io/en/latest/index.html) - 一款简明的sphinx主题

三个扩展组件都需要在`conf.py`文件里进行配置（极其简单的配置）才能发挥作用, 如下

```python
# ... other configurations...

extensions = [
    'sphinxcontrib.mermaid',
    'myst_parser'
]

# ... other configurations...

html_theme = 'sphinx_book_theme'
```

如果你将文档编译成静态文件

```
sphinx-build -b html docs/source/ docs/build/html
```

效果如下：

![sphinx-book-theme的效果图](https://devecor.cn/image/21696a5e-1359-49d3-aef1-e36f706ee8ee/image.png)


### 使用sphinx组织文档象限

按照文档象限的理念，我们需要使得我们的文档具有下列结构

* tutorials
  * create your project
  * deploy your app to cloud
* how-to guides
  * get started with xxx
* reference
  * module index
* explanation
  * architecture decisions

因此假定我们编写的文档目录结构是：

```
├── explanation
│   ├── api-design.md
│   ├── apitest.md
│   ├── architecture.md
│   ├── infrastructure.md
│   └── spike-aws-lambda.md
├── how-to-guides
│   └── development-guide.md
└── reference
    └── index.md
```

笔者发现利用sphinx的`toctree`指令，可以轻易实现文档象限需要的效果。
在`source`文件夹下创建四个目录，分别命名为tutorials，how-to guides， reference 和 explanation来放置你的文档
在`index.rst`文件里声明四个`toctree`, 这将被渲染成树形结构的文档标题列表，下面是一个例子

```
Welcome to example's documentation!
=====================================

.. toctree::
   :maxdepth: 2
   :caption: Explanation:

   explanation/architecture
   explanation/spike-aws-lambda
   explanation/apitest
   explanation/api-design
   explanation/infrastructure


.. toctree::
   :maxdepth: 2
   :caption: How-to Guides:

   how-to-guides/development-guide


.. toctree::
   :maxdepth: 2
   :caption: Reference:

   reference/index
```

> :memo: **Note:** `index.rst` 中的相对路径是基于 `docs/source` 的路径.

> :memo: **Note:** `index.rst`采用的是`reStructuredText`标记语法，实践中可按需更换成`markdown`

渲染效果，可参考[这里](https://devecorsoft.github.io/tinyoauth/)

值得一提的是，reference文档通常不是开发者手动编写而来的，而是由工具生成而来。如何集成到我们的文档系统是个问题，可能的选择比较多，一个普适性较强同时也是笔者推荐的方式是：
1. 利用工具生成html
2. 与sphinx生成的html合并, 例如`cp html build/html/reference`
3. 在`reference/index.md`中加入链接指向reference的html，例如`# [Module Index](reference)`

### 部署你的sphinx文档

多数情况下，使用sphinx的目的之一是生成可部署或托管的静态文件。尽管sphinx同时支持生成pdf，电子书或者图片等，我们却很少用到这些功能。

常用的部署方式：
* 国内云厂商的对象存储服务，AWS的S3储存桶
* 各大代码托管平台提供`pages`托管服务，比如`Github pages`
* 自有服务器上部署

> :memo: **Note:** 关于部署的实现细节超出了本篇博客的范畴

[^1]: [Divio documentation system](https://documentation.divio.com/introduction/)
[^2]: 如果你不熟悉Python，你很有可能不需要花力气去安装它，对于Mac用户和大多数Linux用户来讲，Python是预装在操作系统里的，使用`python3 -V`即可查看你当前是否安装了python解释器。注意在这个方案下你可能需要键入`python3`而不是`python`。对于windows用户，你需要从[python官方网站](https://www.python.org/downloads/)下载并安装。
[^3]: 有时候你会关掉terminal，重新打开后，conda默认会切回`base`环境，需要时重新激活docs环境： `conda activate docs`
