## Web version for ruDocs

Веб-версия заметок по PowerShell, командам Linux и инструментам DevOps из репозитория [ruDocs](https://github.com/Lifailon/rudocs), а также синтаксису [Go](https://github.com/Lifailon/golang-cheat-sheet-ru) и [Node.js](https://github.com/Lifailon/node.js-cheat-sheet-ru).

## Branches

👉 Статья на Хабр: [Создание статических сайтов из Markdown без HTML]()

| **Branch**                                                                                      | **Engine**                                                                                                              |
| -                                                                                               | -                                                                                                                       |
| **[main](https://github.com/Lifailon/lifailon.github.io/tree/main)**                            | Markdown sources for building                                                                                           |
| ✔️ **[zola-duckquill](https://github.com/Lifailon/lifailon.github.io/tree/zola-duckquill)**     | [Zola](https://github.com/getzola/zola) and [Duckquill](https://codeberg.org/daudix/duckquill) theme                    |
| **[mkdocs-material](https://github.com/Lifailon/lifailon.github.io/tree/mkdocs-material)**      | [MkDocs](https://github.com/mkdocs/mkdocs) and [Material](https://github.com/squidfunk/mkdocs-material) theme           |
| **[hugo-dark](https://github.com/Lifailon/lifailon.github.io/tree/hugo-dark)**                  | [Hugo](https://github.com/gohugoio/hugo) and [Dark Theme Editor](https://github.com/JingWangTW/dark-theme-editor)       |
| **[docsify](https://github.com/Lifailon/lifailon.github.io/tree/docsify)**                      | [Docsify](https://github.com/docsifyjs/docsify) with no build                                                           |
| **[mdbook](https://github.com/Lifailon/lifailon.github.io/tree/mdbook)**                        | [mdBook](https://github.com/rust-lang/mdBook) based on Rust                                                             |
| **[jekyll-now](https://github.com/Lifailon/lifailon.github.io/tree/jekyll-now)**                | [Jekyll](https://github.com/jekyll/jekyll) and [Jekyll Now](https://github.com/barryclark/jekyll-now)                   |
| **[pandoc-solarized](https://github.com/Lifailon/lifailon.github.io/tree/pandoc-solarized)**    | [Pandoc](https://github.com/jgm/pandoc) and Solarized Dark theme                                                        |

## Build for Zola

[![Build](https://github.com/Lifailon/lifailon.github.io/actions/workflows/build-zola.yml/badge.svg)](https://github.com/Lifailon/lifailon.github.io/actions/workflows/build-zola.yml)

```PowerShell
# Clone this repository and duckquill theme
git clone https://github.com/Lifailon/lifailon.github.io
Set-Location lifailon.github.io
git clone https://codeberg.org/daudix/duckquill.git themes/duckquill

# Download Zola
Invoke-RestMethod "https://github.com/getzola/zola/releases/download/v0.19.2/zola-v0.19.2-x86_64-pc-windows-msvc.zip" -OutFile zola.zip
Expand-Archive -Path zola.zip && Remove-Item zola.zip

# Start server and build site
.\zola serve
.\zola build

# Copy files for public
Copy-Item public $env:TEMP -Recurse -Force
git switch zola-duckquill
Remove-Item * -Force -Recurse -Exclude .git
Move-Item $env:TEMP\public\* .\
```