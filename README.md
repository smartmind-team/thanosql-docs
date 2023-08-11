# ThanoSQL Docs

ThanoSQL Technical Documentation - [https://docs.thanosql.ai](https://docs.thanosql.ai)

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

## Additional Information

The documentation is powered by [MkDocs](https://www.mkdocs.org/), a fast and simple static site generator.

If you are a member of smartmind, and want more information about mkdocs check [Clickup](https://app.clickup.com/31080411/v/dc/xmfyv-1363/xmfyv-8363)
