# 文档象限

------------
## 序

> 你的软件产品如何优秀对用户来说并不是最重要的，因为你的文档如果不够优秀，用户不会使用它！即便用户在某些情况下不得不使用你的产品，没有好的文档，用户无法高效使用或者以错误的方式使用你的产品

这就是我们要用文档象限的理念来组织我们的产品文档

---------------
## 实践参考

### sphinx

注意，这里不是在讨论古埃及的狮身人面像:blush:，`sphinx`是笔者见过的最好的文档工具，该工具最初是用来创建`Python`官方文档的，同时也很适合许多其他语言的软件项目的文档化。

本文我们将探讨如何在`sphinx`这个工具上落地。

#### 浅接触sphinx

`sphinx`作为Python的一个第三方lib发行给用户。所以你得安装一个python的解释器。稍安勿躁，针对两种不同的人群，笔者准备了两种方案帮你安装它：

如果你熟悉Python（最基本的了解即可），笔者推荐使用[miniconda](https://docs.conda.io/en/latest/miniconda.html)管理你的python环境。当然，你尽可以选择你喜欢的方式。

安装完miniconda之后，这个命令`conda -V`能显示miniconda的版本号，例如`conda 4.12.0`

接下来为sphinx文档启用专用的python环境

```sh
conda create --name docs  python=3.10
conda activate docs
```

> :memo: **Note:** 当你重新打开终端时，conda默认会切回`base`环境，需要时重新激活docs环境： `conda activate docs`

如果你不熟悉Python，你很有可能不需要花力气去安装它，对于Mac用户和大多数Linux用户来讲，Python是预装在操作系统里的，使用`python3 -V`即可查看你当前是否安装了python解释器。对于windows用户，你需要从[python官方网站](https://www.python.org/downloads/)下载并安装。

当你有了python解释器后，就能够安装sphinx及其扩展，保存下面的依赖到`requirements.txt`文件，便能使用`python -m pip install -r requirements.txt`安装它们。

```
sphinx >= 4.5.0
sphinxcontrib-mermaid >= 0.7.1
myst-parser >= 0.17.2
sphinx-book-theme >= 0.3.2
```

> :memo: **Note:** 你可能需要键入`python3`而不是`python`

是时候初始化我们的文档了:
```
sphinx-quickstart docs
```
这会生成一个docs目录，[sphinx官方](https://www.sphinx-doc.org/en/master/tutorial/getting-started.html)已经给出了足够优秀的说明，本文不再赘述。

看到这里你可能会疑惑上文中`requirements.txt`多余三个依赖包是干什么用的，让我一一解释：

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

#### 使用sphinx组织文档象限


### markdown + github

`Github`作为全球最大的代码托管平台，对markdown的支持不可谓不好，近来，`Github`加入对`mermaid`绘图和常用图片显示的支持, 使得`markdown`在github上表现能力大幅提升。

利用github本身作为文档系统已经成为一个不错的选择。

folders
  * tutorials
  * how-to guides
  * reference
  * explanation
