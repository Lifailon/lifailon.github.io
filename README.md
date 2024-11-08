<h3 align="center">
    Web version of the
    <a href="https://github.com/Lifailon/PS-Commands">PowerShell Commands</a>
    and
    <a href="https://github.com/Lifailon/rudocs">RuDocs</a>
    <br></br>
</h3>

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

- [Markdown Preview Enhanced](https://github.com/shd101wyy/vscode-markdown-preview-enhanced) - предпросмотр, а также конвертация в формат `PDF` и `HTML`.
- [Markdown All in One](https://github.com/yzhang-gh/vscode-markdown) - быстрое создание меню, а также ряд других функций.

---

## Build Zola

```shell
### Clone repository and theme
git clone lifailon.github.io
cd lifailon.github.io
git clone https://codeberg.org/daudix/duckquill.git themes/duckquill

### Install zola
Invoke-RestMethod "https://github.com/getzola/zola/releases/download/v0.19.2/zola-v0.19.2-x86_64-pc-windows-msvc.zip" -OutFile zola.zip
Expand-Archive -Path zola.zip
Remove-Item zola.zip

.\zola.exe serve
.\zola.exe build
```