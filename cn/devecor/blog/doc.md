# Practice reference for documentation quadrants

Practicing a handy way to ensure the right documentation is being produced.

## Introduction

> It doesn’t matter how good your product is, because if its documentation is not good enough, people will not use it. Even if they have to use it because they have no choice, without good documentation, they won’t use it effectively or the way you’d like them to.

That's the reason why we need to follow [document quadrants](https://documentation.divio.com/introduction/) pattern.


> Documentation needs to include and be structured around its four different functions: **tutorials, how-to guides, technical reference and explanation**. Each of them requires a distinct mode of writing.
>
|             | Tutorials                          | How-to guides                        | Reference                         | Explanation                           |
| ----------- | ---------------------------------- | ------------------------------------ | --------------------------------- | ------------------------------------- |
| oriented to | learning                           | a goal                               | infomation                        | understanding                         |
| must        | allow the newcomer to get started  | show how to solve a specific problem | describe the machinery            | explain                               |
| its form    | a lesson                           | a series of steps                    | dry description                   | discursive explanation                |
| analogy     | teaching a small child how to cook | a recipe in a cookery book           | a reference encyclopaedia article | an article on culinary social history |

Documentation quadrants looks straightforward, right?

## Contents

- [Practice reference for documentation quadrants](#practice-reference-for-documentation-quadrants)
  - [Introduction](#introduction)
  - [Contents](#contents)
  - [Tutorials](#tutorials)
    - [sphinx](#sphinx)
      - [Get started with sphinx](#get-started-with-sphinx)
      - [Organize your documentation with sphinx](#organize-your-documentation-with-sphinx)
      - [Deploy your sphinx docs](#deploy-your-sphinx-docs)
    - [Markdown in github](#markdown-in-github)

## Tutorials

### sphinx

> :memo: **Note:** this is not a discussion of the Sphinx in ancient Egypt:blush:.

[sphinx](https://www.sphinx-doc.org/en/master/) is the best document generation tool as far as I know. It was originally created for the Python documentation. And it has excellent facilities for the documentation of software projects in a range of languages.

we're gonna talk about the practice for document quadrants with sphinx tool.

#### Get started with sphinx

`sphinx` is been distributed to public as a 3rdparty library for `Python`. So you have to install a Python interpreter. Don't worry, I prepared two solutions to keep it simple no matter if you are familiar with Python.

If you are already a `Python` user, I highly recommend using [miniconda](https://docs.conda.io/en/latest/miniconda.html) to manage your python environment. It allows you to have multiple versions of python interpreters that are isolated from each other.

You can test your miniconda installation with the command `conda -V`, and it will show the installed version.

Once you have the `conda`, you can setup a dedicated python interpreter for sphinx tool:

```sh
conda create --name docs  python=3.10
conda activate docs
```

> :memo: **Note:** By default, miniconda will fallback to `base` interpreter if you reopen terminal. we have to activate `docs` environment once again with `conda activate docs`.

Even if you havn't ever used `Python`, you probably an't gonna need to install a Python interpreter! By default, it's preinstalled for Mac users and most of Linux users. You can check it by `python3 -V`. But for windows users, you have to download it from [official website](https://www.python.org/downloads/).

Once you have python interpreter, it become so easy to install sphinx and its extensions. Try to save them in `requirements.txt` file as follows, then you can install them with `python -m pip install -r requirements.txt`.

```
sphinx >= 4.5.0
sphinxcontrib-mermaid >= 0.7.1
myst-parser >= 0.17.2
sphinx-book-theme >= 0.3.2
```

> :memo: **Note:** You probably need to type `python3` instead of `python` sometimes.

Now, everything is all right, we are able to initialize our documentation:

```sh
sphinx-quickstart docs
```

This will generate several files in `docs` folder. and [sphinx official doc](https://www.sphinx-doc.org/en/master/tutorial/getting-started.html) give your some explanations about them. Please take time to read it.

You're probably confused with so many dependencies in `requirements.txt`. Each of them is optional but quite useful:

* [myst-parser](https://myst-parser.readthedocs.io/en/latest/index.html) - By default, sphinx's plaintext markup language is `reStructuredText (reST) ` instead of `markdown`. And this extension has the capability of replacing `reST` with `markdown`
* [sphinxcontrib-mermaid](https://myst-parser.readthedocs.io/en/latest/intro.html#extending-sphinx) - [Mermaid.js](https://mermaid-js.github.io/mermaid/#/) supporting! It's pretty cool to draw a diagram with plaintext in markdown, e.g. you type the follow code block:
  ````
  ```{mermaid}
  flowchart LR
    A --> B
  ```
  ````

  And this will become:

  ```mermaid
  flowchart LR
    A --> B
  ```

* [sphinx-book-theme](https://sphinx-book-theme.readthedocs.io/en/latest/index.html) - a theme that looks brief and clear.

These three components (two extensions and one theme) need to be configured into `conf.py`.

```python
# ... other configurations...

extensions = [
    'sphinxcontrib.mermaid',
    'myst_parser'
]

# ... other configurations...

html_theme = 'sphinx_book_theme'
```

Then you can generate your docs into `html`:

```
sphinx-build -b html docs/source/ docs/build/html
```

it looks pretty good.

<img src="https://devecor.cn/image/21696a5e-1359-49d3-aef1-e36f706ee8ee/image.png" style="max-width:100%;"/>

#### Organize your documentation with sphinx

As the principle of document quadrants, we'd better to structure our documentation with four functionalities. e.g.

* tutorials
  * create your project
  * deploy your app to cloud  
* how-to guides
  * get started with xxx
* reference
  * module index
* explanation
  * architecture decisions

Assume that we have documentations with this structure:

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

I noticed that `toctree` directive in sphinx is adequate for our needs. Just declare four `toctree`s in `index.rst` which is entry point of sphinx.

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

> :memo: **Note:** As it's memtioned above, `index.rst` is a `reST` file, we can replace it by `markdown` on demand. However, it's simple enough, No reason to change it in my opinion.

Here is a real-life [example](https://devecorsoft.github.io/tinyoauth/) to preview.

#### Deploy your sphinx docs

The main reasion why we use doc generation tool such as sphinx is to deploy our documentation as a website. Even thought sphinx has ability to generate pdf, e-book, image and etc, we rarely use these functions.

There are so many choices for deploying:

* AWS S3 bucket
* Github pages
* server
...

> :memo: **Note:** the details of how-to deploy are beyound this blog.
    
### Markdown in github

As the most popular code hosting platform for developers, `Github`'s support for markdown is not bad. Particularly, `Github` has added support for `mermaid` drawing and common image display in the past years, making `markdown` on github much more presentable. It can be a good "documentation tool" requiring only four folders.

folders
  * tutorials
  * how-to guides
  * reference
  * explanation

You probably want a `README.md` listing your "Content index" as the entry point. e.g.

[![image](https://devecor.cn/image/ab5f8806-c615-4159-a418-26f717d320ae/image.png)](https://github.com/DevecorSoft/upimage/tree/main/docs)
