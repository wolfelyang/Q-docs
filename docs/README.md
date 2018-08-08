---
description: >-
  Useful guides for various roles: analysts, developers, scientists, and
  administrators.
---

# Guides to Q

Maana product documentation organized by role:

- [Analyst Guide](docs/analyst/index.md)
- [Developer](docs/developer/index.md)
- [Scientist](docs/scientist/index.md)
- [Adinistrator](docs/administrator/index.md)

## Build

Install mkDocs using [PIP](https://packaging.python.org/tutorials/installing-packages/):

```bash
pip install mkdocs
```

To produce a static site, within the root direcotry, simply:

```bash
mkdocs build
```

## Serve

To test the site:

```bash
mkdocs serve
```

## Deploy

To update GitHub's live view:

```bash
mkdocs gh-deploy
```
