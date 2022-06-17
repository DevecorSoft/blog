# Practice the documentation quadrants on `sphinx`

Practicing a handy way to ensure the right documentation is being produced.

## Introduction

> It doesn’t matter how good your product is, Because if its documentation is not good enough, people will not use it. Even if they have to use it because they have no choice, without good documentation, they won’t use it effectively or the way you’d like them to.

That's the reason why we need to follow the [document quadrants](https://documentation.divio.com/introduction/) pattern.


> Documentation needs to include and be structured around its four different functions: **tutorials, how-to guides, technical reference and explanation**. Each of them requires a distinct mode of writing.
>
|             | Tutorials                          | How-to guides                        | Reference                         | Explanation                           |
| ----------- | ---------------------------------- | ------------------------------------ | --------------------------------- | ------------------------------------- |
| oriented to | learning                           | a goal                               | information                        | understanding                         |
| must        | allow the newcomer to get started  | show how to solve a specific problem | describe the machinery            | explain                               |
| its form    | a lesson                           | a series of steps                    | dry description                   | discursive explanation                |
| analogy     | teaching a small child how to cook | a recipe in a cookery book           | a reference encyclopaedia article | an article on culinary social history |

Documentation quadrants look straightforward, right?

Using a document generation tool can make life easier, rather than develop a website. [sphinx](https://www.sphinx-doc.org/en/master/) is the best document generation tool as far as I know. It was originally created for the Python documentation. And it has excellent facilities for the documentation of software projects in a range of languages.

we're gonna talk about the practice for document quadrants with the sphinx tool.

## Contents

- [Practice the documentation quadrants on `sphinx`](#practice-the-documentation-quadrants-on-sphinx)
  - [Introduction](#introduction)
  - [Contents](#contents)
  - [Tutorials](#tutorials)
    - [Get started with sphinx](#get-started-with-sphinx)
    - [Organize your documentation with sphinx](#organize-your-documentation-with-sphinx)
    - [Deploy your sphinx docs](#deploy-your-sphinx-docs)

## Tutorials

### Get started with sphinx

`sphinx` has been distributed to public as a 3rd-party library for `Python`. So you have to install a Python interpreter.

If you are already a `Python` user, I highly recommend using [miniconda](https://docs.conda.io/en/latest/miniconda.html) to manage your python environment. It allows you to have multiple versions of python interpreters that are isolated from each other. [^1]

You can test your miniconda installation with the command `conda -V`, and it will show the installed version.

Once you have the `conda`, you can setup a dedicated python interpreter for sphinx tool: [^2]

```sh
conda create --name docs  python=3.10
conda activate docs
```

Once you have a python interpreter, it becomes so easy to install sphinx and its extensions. Try to save them in `requirements.txt` file as follows, then you can install them with `python -m pip install -r requirements.txt`.

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

This will generate several files in the `docs` folder. And the [sphinx official doc](https://www.sphinx-doc.org/en/master/tutorial/getting-started.html) gives you some explanations about them. Please take some time to read it.

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

It looks pretty good.

![preview sphinx-book-theme](https://devecor.cn/image/21696a5e-1359-49d3-aef1-e36f706ee8ee/image.png)

### Organize your documentation with sphinx

As the principle of document quadrants, we're supposed to structure our documentation with four functionalities. e.g.

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

The `toctree` directive in sphinx is adequate for our needs. Just declare four `toctree`s in `index.rst` which is the entry point of sphinx.

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

> :memo: **Note:** As it's mentioned above, `index.rst` is a `reST` file, we can replace it by `markdown` on demand. However, it's simple enough, No reason to change it in my opinion.

Here is a real-life [example](https://devecorsoft.github.io/tinyoauth/) to preview.

Particularly worth mentioning is that `reference` is usually not written manually by the developer, but generated by some tools such as `javadoc` and `jsdoc`. How to integrate with sphinx can be a problem. We have to weigh up the pros and cons carefully, although there are many options. A general approach (recommended) is:

1. generate `html` with the tool you are using.
2. merge it into sphinx build directory such as `cp html build/html/reference`
3. add link in `reference/index.md` e.g. `# [Module Index](reference)`

### Deploy your sphinx docs

The main reason why we use doc generation tools such as sphinx is to deploy our documentation as a website. Even though sphinx has the ability to generate pdf, e-book, image, etc. we rarely use these functions.

There are so many choices for deploying:

* AWS S3 bucket
* Github pages
* server
...

> :memo: **Note:** the details of how-to deploy are beyond this blog.

[^1]: Even if you haven't ever used `Python`, you probably ain't gonna need to install a Python interpreter! By default, it's preinstalled for Mac users and most Linux users. You can check it by executing the command `python3 -V`. But for Windows users, you have to download it from the [official website](https://www.python.org/downloads/).

[^2]: By default, miniconda will jump to the `base` interpreter if you close the terminal and reopen it. We have to activate the `docs` environment once again with the command `conda activate docs`.
