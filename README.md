# Production Engineering from DevOps to MLOps

This repository contains the English source files for **Production Engineering from DevOps to MLOps** book.

You may browse its content [here](chapters/).

This book is also available on [Leanpub](https://leanpub.com/devopsmlops) (PDF, EPUB).

## Development notes

You can create this book by using the Makefile provided here. This uses [Pandoc](https://pandoc.org/). If you want to build the project, create a simlink to the images or just copy the images directory to the root.

```bash
cp -r chapters/images .
make
```

Or

The online version of the book is powered by [Material for Mkdocs](https://squidfunk.github.io/mkdocs-material/). To deploy it locally, clone or download this repository, install [poetry](https://python-poetry.org/) then run the following commands in the cloned/downloaded folder:

```bash
poetry shell
poetry install
mkdocs serve
```

By default, the book is deployed at <http://localhost:8000> on the local machine.

## License

This work is published under a [Creative Commons](LICENSE) license. All code is licensed under an [MIT](CODE_LICENSE) license. [Contributions](CONTRIBUTING.md) are most welcome.

Copyright © 2023-present [Arnab Bose] and [Sebastien Donadio].
