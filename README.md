# latex-styles

LuaLaTeX 向けの再利用可能な個人用スタイル集です。スタイル・サンプル・スタイル固有テストを管理し、コンパイル環境そのものは含みません。

本リポジトリは [`latex-env`](https://github.com/Tsuboi-coder/latex-env) が提供する Docker 環境に対して開発・テストしています。依存関係は次の一方向です。

```text
latex-styles ── tested against / requires ──> latex-env のビルド環境
```

`latex-env` は本リポジトリなしでも利用できます。また、下記の要件を満たす同等の環境であれば `latex-env` 以外でもスタイルを利用できます。

## 必要な環境

- LuaLaTeX
- TeX Live 2026
- `latexmk`
- LuaTeX-ja
- Python 3 と Pygments（`minted` を使う文書）
- `-shell-escape`（`minted` を使う文書だけ）
- macOS システムフォントのマウント（一部スタイルだけ）

## 構成

```text
latex-styles/
├── texmf/tex/latex/latex-styles/  # パッケージ本体
├── examples/
│   └── document/
│       ├── book1/           # 書籍スタイルの使用例
│       └── article1/        # 通常文書スタイルの使用例
├── tests/
│   ├── document/            # 書籍・通常文書の動作確認
│   └── beamer/              # Beamer の動作確認
└── docs/                    # フォント設定などの補足
```

主なパッケージとプラットフォーム依存は次のとおりです。

| パッケージ | 用途 | macOS フォント |
| --- | --- | --- |
| `book1` | 日本語・英語書籍の言語、余白、フォントを組み合わせるプリセット | ヒラギノ、または Times New Roman + Helvetica Neue（`font=none` では不要） |
| `article1` | 日本語・英語articleの言語、余白、フォントを組み合わせるプリセット | ヒラギノ、または Times New Roman + Helvetica Neue（`font=none` では不要） |
| `hiragino-base` | 通常文書のヒラギノプリセット | 必要 |
| `hiragino-slides` | Beamer のヒラギノプリセット | 必要 |
| `beamer-design-ff-aug` | Frankfurt ベースの Beamer デザイン | 必要 |
| `marp-beamer-design-flow` | Marp と寸法を揃えた Beamer デザイン | 任意（フォールバックあり） |

`teststyle.sty` は数式・化学式の共有 TEXMF 読み込みを確認するテスト用パッケージです。

## `latex-env` から使う

`LATEX_STYLES_ROOT` には、このリポジトリ直下の `texmf` を指定します。

```shell
export LATEX_STYLES_ROOT="$HOME/Documents/Repository/latex-styles/texmf"
```

その後、文書のあるディレクトリから `latex-env` のラッパーを呼び出します。

```shell
/absolute/path/to/latex-env/scripts/latexmk-docker -lualatex document.tex
```

文書からは通常のパッケージと同様に読み込めます。

```tex
\usepackage[language=japanese,layout=standard]{book1}
```

通常のarticleでは次のように読み込みます。タイトル、著者名、日付は本文1ページ目の冒頭に表示され、独立した表紙ページにはなりません。タイトルはサンセリフになり、英語ではHelvetica Neue、日本語ではヒラギノ角ゴシックを使用します。さらに英語設定では、partからsubparagraphまでの全見出しと、目次内のpart・section項目もHelvetica Neueになります。

```tex
\documentclass[11pt,a4paper]{article}
\usepackage[language=english,layout=standard]{article1}

\title{A Sample Article}
\author{Author Name}
\date{\today}

\begin{document}
\maketitle
\tableofcontents
\end{document}
```

`book1` と `article1` の `font=auto`（既定値）は、`language=japanese` ではヒラギノ、
`language=english` では本文に Times New Roman、サンセリフに Helvetica Neue を設定します。
数式フォントには本文フォントを適用せず、既定の Latin Modern 数式フォントを維持します。
文書側でフォントを設定する場合は `font=none` を指定します。

### Visual Studio Code / LaTeX Workshop

GUI から起動した Visual Studio Code は、シェルで `export` した環境変数を引き継がない場合があります。LaTeX Workshop の `latex-workshop.latex.tools` で、`latex-env` のラッパーと本リポジトリの TEXMF ルートを明示してください。

```json
{
  "name": "docker-lualatex",
  "command": "/absolute/path/to/latex-env/scripts/latexmk-docker",
  "env": {
    "LATEX_IMAGE": "latex-env:2026",
    "LATEX_STYLES_ROOT": "/absolute/path/to/latex-styles/texmf"
  },
  "args": [
    "-lualatex",
    "-shell-escape",
    "-synctex=1",
    "-interaction=nonstopmode",
    "-file-line-error",
    "%DOCFILE_EXT%"
  ]
}
```

設定後は **Developer: Reload Window** を実行し、LaTeX Workshop の Docker 用レシピで再ビルドします。完全な `settings.json` と強制再ビルドの方法は、[`latex-env` のセットアップ手順](https://github.com/Tsuboi-coder/latex-env/blob/main/docs/setup.md#visual-studio-code)を参照してください。

## 動作確認

以下は両リポジトリが現在の配置にある場合の例です。

```shell
export LATEX_STYLES_ROOT="$HOME/Documents/Repository/latex-styles/texmf"

cd "$HOME/Documents/Repository/latex-styles/tests/document"
"$HOME/latex-env/scripts/latexmk-docker" -lualatex shared_style_math.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex book1_japanese_wide.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex book1_english_wide.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex article1_japanese_wide.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex article1_english_wide.tex

cd ../beamer
"$HOME/latex-env/scripts/latexmk-docker" -lualatex hiragino_beamer.tex

cd ../../examples/document/book1/11pt
"$HOME/latex-env/scripts/latexmk-docker" -lualatex japanese_book.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex english_book.tex

cd ../../article1/11pt
"$HOME/latex-env/scripts/latexmk-docker" -lualatex japanese_article.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex english_article.tex
```

書籍・articleサンプルはmacOSフォントを参照します。必要なファイルとDocker Desktopの設定は [docs/hiragino-fonts.md](docs/hiragino-fonts.md) を参照してください。

## 互換性

| latex-styles | latex-env | TeX Live |
| --- | --- | --- |
| `main`（リポジトリ分離後） | `main`（リポジトリ分離後） | 2026 |

TeX Live の更新でクラスやパッケージの挙動が変わる場合があります。リリース時には、動作確認した `latex-env` と `latex-styles` の組み合わせをタグで記録します。

## ライセンス

このプロジェクトは [MIT License](LICENSE) のもとで公開しています。
