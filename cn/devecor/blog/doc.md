# Practice reference for documentation quadrants

Practicing a handy way to ensure the right documentation is being produced.

## Introduction

> It doesn’t matter how good your product is, because if its documentation is not good enough, people will not use it. Even if they have to use it because they have no choice, without good documentation, they won’t use it effectively or the way you’d like them to.

That's the reason we need to follow [document quadrants](https://documentation.divio.com/introduction/) pattern.


> Documentation needs to include and be structured around its four different functions: **tutorials, how-to guides, technical reference and explanation**. Each of them requires a distinct mode of writing.
>
|             | Tutorials                          | How-to guides                        | Reference                         | Explanation                           |
| ----------- | ---------------------------------- | ------------------------------------ | --------------------------------- | ------------------------------------- |
| oriented to | learning                           | a goal                               | infomation                        | understanding                         |
| must        | allow the newcomer to get started  | show how to solve a specific problem | describe the machinery            | explain                               |
| its form    | a lesson                           | a series of steps                    | dry description                   | discursive explanation                |
| analogy     | teaching a small child how to cook | a recipe in a cookery book           | a reference encyclopaedia article | an article on culinary social history |

Documentation quardrants looks straightforward, right?

## Contents

- [Practice reference for documentation quadrants](#practice-reference-for-documentation-quadrants)
  - [Introduction](#introduction)
  - [Contents](#contents)
  - [Tutorials](#tutorials)
    - [sphinx](#sphinx)
      - [Get started with sphinx](#get-started-with-sphinx)
    - [Markdown in github](#markdown-in-github)

## Tutorials

### sphinx

> :memo: **Note:** this is not a discussion of the Sphinx in ancient Egypt:blush:.

[sphinx](https://www.sphinx-doc.org/en/master/) is the best document generation tool as far as I know. It was originally created for the Python documentation, and it has excellent facilities for the documentation of software projects in a range of languages.

we gonna talk about the practice for document quadrants with sphinx tool.

#### Get started with sphinx

`sphinx` is been distributed to public as a 3rdparty library for `Python`. So you have to install a Python interpreter. Don't worry, we prepared two solutions to keep it simple no matter if you are familiar with Python.

If you are aready a `Python` user, I highly recommend using [miniconda](https://docs.conda.io/en/latest/miniconda.html) to manage your python envirnments. You can have multiple python interpreters that are isolated from each other.

You can test your miniconda installation with `conda -V`, and it's output like `conda 4.12.0`.

It's time to setup dedicated python interpreter for sphinx tool:

```sh
conda create --name docs  python=3.10
conda activate docs
```

> :memo: **Note:** By default, miniconda will fallback to `base` interpreter is case you reopen terminal. we have to activate `docs` once again: `conda activate docs`.

Even if you havn't ever used `Python`, you probably an't gonna need to install a Python interpreter! By default, it's preinstalled for Mac users and most of Linux users. You can check it by `python3 -V`. But for windows users, you have to download it from [official website](https://www.python.org/downloads/).

Once you have python interpreter, it become so easy to install sphinx and its extensions. Try to save them in `requirements.txt` file as follows, then you can install them with `python -m pip install -r requirements.txt`.

```
sphinx >= 4.5.0
sphinxcontrib-mermaid >= 0.7.1
myst-parser >= 0.17.2
sphinx-book-theme >= 0.3.2
```

> :memo: **Note:** You probably need to type `python3` instead of `python` sometimes.


    
### Markdown in github

folders
  * tutorials
  * how-to guides
  * reference
  * explanation
