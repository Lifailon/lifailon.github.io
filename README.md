## Web version for [PowerShell Commands](https://github.com/Lifailon/PS-Commands)

| **Branch**                                                                                      | **Description**                                                                                                                                     |
| -                                                                                               | -                                                                                                                                                   |
| **[main](https://github.com/Lifailon/lifailon.github.io/tree/main)**                            | Markdown sources for building **MkDocs** and **Zola**.                                                                                              |
| ✔️ **[zola-duckquill](https://github.com/Lifailon/lifailon.github.io/tree/zola-duckquill)**     | Assembled from Markdown using [Zola](https://github.com/getzola/zola) and [Duckquill theme](https://codeberg.org/daudix/duckquill).                 |
| **[mkdocs-material](https://github.com/Lifailon/lifailon.github.io/tree/mkdocs-material)**      | Assembled from Markdown using [MkDocs](https://github.com/mkdocs/mkdocs) and [Material theme](https://github.com/squidfunk/mkdocs-material).        |
| **[hugo-dark](https://github.com/Lifailon/lifailon.github.io/tree/hugo-dark)**                  | Assembled from Markdown using [Hugo](https://github.com/gohugoio/hugo) and [Dark Theme Editor](https://github.com/JingWangTW/dark-theme-editor).    |
| **[jekyll-now](https://github.com/Lifailon/lifailon.github.io/tree/jekyll-now)**                | Assembled from Markdown using [Jekyll](https://github.com/jekyll/jekyll) and [Jekyll Now](https://github.com/barryclark/jekyll-now).                |
| **[pandoc-solarized](https://github.com/Lifailon/lifailon.github.io/tree/pandoc-solarized)**    | Assembled from Markdown using [Pandoc](https://github.com/jgm/pandoc) and Solarized Dark theme.                                                     |

Статья на Хабр: [Создание статических сайтов из Markdown без HTML](https://habr.com/ru/articles/826474)

Используемы расширения **VSCode**:

- [Markdown Preview Enhanced](https://github.com/shd101wyy/vscode-markdown-preview-enhanced) - предпросмотр, а также конвертация в формат `PDF` и `HTML` через `Puppeteer`.
- [Markdown All in One](https://github.com/yzhang-gh/vscode-markdown) - автоматическое создание меню, а также ряд других функций.

### Build

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