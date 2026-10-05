# Tests

テスト文書は、文書クラスの系統ごとに分けています。

```text
tests/
├── document/  # book、ltjsbook、ltjsarticle
└── beamer/    # beamer
```

## document

- `book1_japanese_wide.tex`: `book1` の日本語・wide構成
- `book1_english_wide.tex`: `book1` の英語・wide構成と本文・数式フォント
- `hiragino_document.tex`: `hiragino-base` 単体のフォント設定
- `shared_style_math.tex`: `teststyle` の数式・化学式と共有TEXMFからの読み込み

`hiragino_document.tex` と `shared_style_math.tex` は `book1` と異なる責務を検証するため、
`book1` のテストとは別に残します。

## beamer

- `hiragino_beamer.tex`: `hiragino-slides` の日本語・欧文フォントと数式
