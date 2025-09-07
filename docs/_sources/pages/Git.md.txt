# Git

## Sphinx

Sphinx [Themes](https://sphinx-themes.org/)

[Github](https://github.com/sphinx-doc/sphinx)

[Sphinx Docs](https://www.sphinx-doc.org/en/master/index.html)

```
uv pip install sphinx
```
**Verify installation**
```
(.venv) gowtham@debian:~/Documents/Git/Github/operation_linux$ sphinx-build --version
sphinx-build 8.2.3
```

**To create documentation layout**
```
(.venv) gowtham@debian:~/Documents/Git/Github/operation_linux$ sphinx-quickstart docs
Welcome to the Sphinx 8.2.3 quickstart utility.

Please enter values for the following settings (just press Enter to
accept a default value, if one is given in brackets).

Selected root path: docs

You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
> Separate source and build directories (y/n) [n]: y

The project name will occur in several places in the built documentation.
> Project name: Debian Linux Docs
> Author name(s): Gowtham Mahendran
> Project release []: 1.0

If the documents are to be written in a language other than English,
you can select a language here by its language code. Sphinx will then
translate text that it generates into that language.

For a list of supported codes, see
https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.
> Project language [en]: 

Creating file /home/gowtham/Documents/Git/Github/operation_linux/docs/source/conf.py.
Creating file /home/gowtham/Documents/Git/Github/operation_linux/docs/source/index.rst.
Creating file /home/gowtham/Documents/Git/Github/operation_linux/docs/Makefile.
Creating file /home/gowtham/Documents/Git/Github/operation_linux/docs/make.bat.

Finished: An initial directory structure has been created.

You should now populate your master file /home/gowtham/Documents/Git/Github/operation_linux/docs/source/index.rst and create other documentation
source files. Use the Makefile to build the docs, like so:
   make builder
where "builder" is one of the supported builders, e.g. html, latex or linkcheck.
```

**Docs folder should be there**
```
(.venv) gowtham@debian:~/Documents/Git/Github/operation_linux$ ls
docs  easyroam.sh  README.md
```

**To build**

```
(.venv) gowtham@debian:~/Documents/Git/Github/operation_linux$ sphinx-build -M html docs/source/ docs/build/
Running Sphinx v8.2.3
loading translations [en]... done
making output directory... done
building [mo]: targets for 0 po files that are out of date
writing output... 
building [html]: targets for 1 source files that are out of date
updating environment: [new config] 1 added, 0 changed, 0 removed
reading sources... [100%] index
looking for now-outdated files... none found
pickling environment... done
checking consistency... done
preparing documents... done
copying assets... 
copying static files... 
Writing evaluated template result to /home/gowtham/Documents/Git/Github/operation_linux/docs/build/html/_static/language_data.js
Writing evaluated template result to /home/gowtham/Documents/Git/Github/operation_linux/docs/build/html/_static/basic.css
Writing evaluated template result to /home/gowtham/Documents/Git/Github/operation_linux/docs/build/html/_static/documentation_options.js
Writing evaluated template result to /home/gowtham/Documents/Git/Github/operation_linux/docs/build/html/_static/alabaster.css
copying static files: done
copying extra files... 
copying extra files: done
copying assets: done
writing output... [100%] index
generating indices... genindex done
writing additional pages... search done
dumping search index in English (code: en)... done
dumping object inventory... done
build succeeded.

The HTML pages are in docs/build/html.
```


I have moved the `docs` inside `sphinx` because github can either deploy from `root` or `/docs`. I created a `docs` folder in main dir and copied the contents of `docs/build/html` to `docs`.

We also need to add `.nojekyll` file inside `docs` so that the static files are served.

Since I have written down all the docs in wiki as `markdown`, it is better to include a plugin that reads `.md` files. So install, `myst_parser`.

Add the plugin to the conf.py file

```
extensions = ["myst_parser",]

source_suffix = {
    ".rst": "restructuredtext",
    ".md": "markdown",
}
```

In `index.rst`, include the code to read the pages,
```
.. toctree::
   :maxdepth: 2
   :caption: Contents:
   :glob:

   pages/*
```

Create a folder called `pages` inside `sources` and copy paste all the `.md` files from `wiki`.

Now run sphinx and copy all the files inside `build` to `docs` in the main dir. Add `.nojekyll` file inside `docs/` so that the static files are rendered.
