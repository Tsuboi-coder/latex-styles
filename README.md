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
├── texmf/tex/latex/kazuma/  # パッケージ本体
├── examples/                # 書籍スタイルの使用例
├── tests/                   # 小さな動作確認文書
└── docs/                    # フォント設定などの補足
```

主なパッケージとプラットフォーム依存は次のとおりです。

| パッケージ | 用途 | macOS フォント |
| --- | --- | --- |
| `bookmacro-lua` | 日本語書籍の標準・広幅余白プリセット | 不要 |
| `bookmacro-lua-english` | 英語書籍の標準・広幅余白プリセット | Helvetica Neue が必要 |
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
\usepackage{bookmacro-lua}
```

### Visual Studio Code / LaTeX Workshop

GUI から起動した Visual Studio Code は、シェルで `export` した環境変数を引き継がない場合があります。LaTeX Workshop の `latex-workshop.latex.tools` で、`latex-env` のラッパーと本リポジトリの TEXMF ルートを明示してください。

```json
{
  "name": "docker-lualatex",
  "command": "/Users/kazuma/latex-env/scripts/latexmk-docker",
  "env": {
    "LATEX_IMAGE": "kazuma-latex:2026",
    "LATEX_STYLES_ROOT": "/Users/kazuma/Documents/Repository/latex-styles/texmf"
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

cd "$HOME/Documents/Repository/latex-styles/tests"
"$HOME/latex-env/scripts/latexmk-docker" -lualatex shared_style_math.tex

cd ../examples
"$HOME/latex-env/scripts/latexmk-docker" -lualatex japanese_book.tex
"$HOME/latex-env/scripts/latexmk-docker" -lualatex english_book.tex
```

サンプルのうち `japanese_book.tex` と `english_book.tex` は macOS フォントを参照します。必要なファイルと Docker Desktop の設定は [docs/hiragino-fonts.md](docs/hiragino-fonts.md) を参照してください。

## 互換性

| latex-styles | latex-env | TeX Live |
| --- | --- | --- |
| `main`（リポジトリ分離後） | `main`（リポジトリ分離後） | 2026 |

TeX Live の更新でクラスやパッケージの挙動が変わる場合があります。リリース時には、動作確認した `latex-env` と `latex-styles` の組み合わせをタグで記録します。

## ライセンス

このプロジェクトは [MIT License](LICENSE) のもとで公開しています。
