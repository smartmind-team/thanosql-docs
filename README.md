# ThanoSQL Docs

ThanoSQL Technical Documentation - https://docs.thanosql.ai

The ThanoSQL documentation website provides comprehensive guidance on how to use ThanoSQL. The website is divided into several sections, including an introduction to ThanoSQL, a getting started guide, tutorials, a reference guide, and a frequently asked questions (FAQ) section.

The `Intro` section provides a high-level overview of ThanoSQL, highlighting its features and capabilities. 

The `Getting Started` guide offers step-by-step instructions on how to install and set up ThanoSQL, as well as tips on how to get started using the software.

The `Tutorials` section provides detailed guidance on how to use ThanoSQL for specific tasks, such as creating a database and tables, importing data, running queries, and creating custom functions. Each tutorial includes clear, concise instructions and examples to help users learn how to use ThanoSQL effectively.

The `Reference` section is a comprehensive resource that provides detailed information on all the features and functionality of ThanoSQL. Users can browse the guide to find information on specific commands, functions, and syntax.

The `FAQ` section offers answers to common questions about using ThanoSQL, troubleshooting common issues, and getting help with the software. Users can browse the FAQ section to find answers to their questions or contact support for more assistance.

## Contributing

We welcome contributions to the ThanoSQL documentation! To contribute, follow these steps:
- Fork this repository.
- Make your changes.
- Submit a pull request.

## Run Development Server

### Prerequisites

- [pipenv](https://github.com/pypa/pipenv)

### Setup

```bash
pipenv install
```

### Local Run

```bash
pipenv shell
mkdocs serve
```

To Serve on a Local Wi-Fi Network

```bash
# Get yor local ip address. It will return 192.168.x.x
ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}'
mkdocs serve --dev-addr 192.168.x.x:8000
# Any device using the same Wi-Fi can now access it via 192.168.x.x:8000
```

## Construction

This documentation is powered by [MkDocs](https://www.mkdocs.org/), a fast and simple static site generator. We do not directly write HTML; instead, we write pages in Markdown and later convert them into themed HTML files using MkDocs. There are a few official themes and many other community-generated themes that we can use. You can also create your own theme by making use of the extensions provided (more on this in the official MkDocs documentation).

While developing, we can see a live preview of the website using the command

```bash
mkdocs serve
```

By default, the site will be hosted in 127.0.0.1:8000. However, setting `dev_addr` in `mkdocs.yml` will change this address. `mkdocs serve` also support live reloading; opened tabs are automatically refreshed to reflect new changes.

Another thing to remember is that the generated site has a slightly different file structure than our original Markdown files. For example, the original file structure for `query_manager.md` looks like this:

```bash
.
└── docs/
    └── en/
        └── getting_started/
            └── paas/
                └── workspace/
                    ├── index.md
                    ├── lab.md
                    └── query_manager.md
```

However, the file structure of the generated site looks like this:

```bash
.
└── site/
    └── en/
        └── getting_started/
            └── paas/
                └── workspace/
                    ├── lab/
                    │   ├── index.html
                    │   └── lab.md
                    ├── query_manager/
                    │   ├── index.html
                    │   └── query_manager.md
                    ├── index.html
                    └── index.md
```

Notice the additional `query_manager/` folder between `workspace/` and `query_manager.md`, but the same path for `index.md` and the generated `index.html`. Because of this, for files other than those named `index.html`, their relative paths are going to be one directory higher than those of the original. This directory change does not apply to images, however. Thus, the relative paths of images and `index.html` files are going to be the same as those of the original.

For more information, check out the [official MkDocs documentation](https://www.mkdocs.org/).

## Versioning

We keep multiple versions of the documentation using [mike](https://github.com/jimporter/mike), a Python utility built on top of MkDocs. Similar to MkDocs, we use

```bash
mike serve
```

to develop with mike. However, mike does not seem to support live reloading, unlike MkDocs. For convenience, it might be preferable to still develop using `mkdocs serve`. We simply need to ignore the warning `GET /versions.json HTTP/1.1" code 404`.

All changes created will stay in a certain branch until we deploy them by calling `mike deploy`. The default deployment branch `gh-pages` hosts the generated site. A new folder for the version will be created; all site materials for that version are self-contained within the folder. To keep some consistency during development, it's good to work on a version branch until everything is ready for deployment. Changes will stay local until they are pushed to a remote repository using the `--push` flag. Each time it is used, the remote repository will be updated.

To view the list of of all currently-deployed versions, run

```bash
mike list
```

In order to set an alias (such as "latest", "stable", or "beta"), we use the command

```bash
mike alias [identifier] [alias]
# for example: mike alias 2.0 stable
```

We pass the flag `-u` or `--update-aliases` to `deploy` in order to update aliases while making a new deployment. For instance, in order to deploy changes for version 1.5, reflect them in the remote `gh-pages`, and make it the latest version, we use the command

```bash
mike deploy --push --update-aliases 1.5 latest
```

In order to set the default version, where users are directed to from the root page, use

```bash
mike set-default [identifier]
# for example: mike set-default latest
```

To delete a version, run

```bash
mike delete [identifier]
# for example: mike delete 1.2
```

The version folder will be deleted from the repository.

For more information, check out the [official mike documentation on Github](https://github.com/jimporter/mike).

## Additional Information

If you are a member of smartmind, and want more information about mkdocs check [Clickup](https://app.clickup.com/31080411/v/dc/xmfyv-1363/xmfyv-8363)
