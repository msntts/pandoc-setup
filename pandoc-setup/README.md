# Pandoc+VSCodeを使ってMarkdown(Pandoc拡張)からDocxを生成する
## 必要なもの(for windows)
\*がついてるのは、なくてもいい  

| 名前 | 備考 |
|---|---|
| [Pandoc](https://pandoc.org/installing.html) | |
| [Pandoc-crossref](https://github.com/lierdakil/pandoc-crossref/releases) | Pandocのfilter |
| [VSCode](https://code.visualstudio.com/)  | |
| [Markdown Preview Enhanced](https://shd101wyy.github.io/markdown-preview-enhanced/#/) | VSCodeのExtension。インストールはVSCodeからしたほうが楽 |
| [*PlantUML](https://plantuml.com/ja/download) | MarkdownでUMLを書くために必要 |
| [*GraphBiz](https://www.graphviz.org/download/) | PlantUMLを動かすのに必要 |
| *Java | PlantUMLを動かすのに必要 |
| [*Image Magick](https://imagemagick.org/script/download.php) | PlantUMLで書いたUMLをWordに出力するため(画像化する)に必要 |

__!!!注意!!!!__  
Pandoc-crossrefはビルドした時のPandocのバージョンが一致していないと動かない。  
Pandocインストール後、コマンドプロンプトで以下を実行し、バージョンを確認してからPandoc-crossrefの該当モジュールをDLする。

```sh
pandoc --version
```

動作確認した組み合わせは  

- Pandoc v2.9.2.1  
- Pandoc-crossref v0.3.6.2a  
  - v0.4.0.0-alpha6dもPandoc v2.9.2.1とビルドしてると書いていたけど、うまく動かなかった(◞‸◟)。。(2020/05/01時点)

## 事前準備
Pandoc、VSCode、(PlantUML、GraphBiz、Java、Image Magick)をDL&インストールする。  
Pandoc-crossrefはDL後、zip展開しておく。

### Pandoc
- Pandocインストール後(デフォルトだと%USERPROFILE%\\AppData\\Local\\Pandoc)にDLしたPandoc-crossref(ファイル名はpandoc-crossref.exe)をコピー
- Pandoc、GraphBiz、ImageMagickのPATHを通す
- 出力するWordスタイルのテンプレートを出力し、編集
  - Powershellでやったらdocxが壊れていたので、コマンドプロンプトから実行したほうがいい
```sh
pandoc --print-default-data-file reference.docx > reference.docx
```

### VSCode
- Extensionから"Markdown Preview Enhanced"を検索し、インストール
  - VSCodeデフォルトのMarkdown Previewのショートカット(Ctrl-K V、Ctrl-Shift-V)が上書きされるので、Pandocと心中する気がないなら設定を変えておいたほうがいい 
- (AA的な要素が多いので)等幅フォントを使用するよう設定
  - Flie->Preferences->Settings->Editor: Font Fammily->先頭に等幅フォントを設定
  - Windowsにデフォルトでインストールされている等幅フォントは'ＭＳ ゴシック'

#### Markdown Preview Enhanced
- Extensionの設定をデフォルトから以下の項目を変更(注：以下の設定はPreview用の設定。Word変換はまた別のところで)
  - Markdown-preview-enhanced: Pandoc Arguments
    - --filter=pandoc-crossref
  - Markdown-preview-enhanced: Pandoc Markdown Flavor
    - markdown
  - Markdown-preview-enhanced: Use Pandoc Parser
    - Enable

settings.jsonだとこんな感じ

```json
    "markdown-preview-enhanced.pandocMarkdownFlavor": "markdown",
    "markdown-preview-enhanced.usePandocParser": true,
    "markdown-preview-enhanced.pandocArguments": "--filter=pandoc-crossref",
```

## 執筆
markdownの先頭にPandocでWordを出力するための設定と、表とか図のキャプションをMarkdownの先頭に記述しておく。そこからは好きに書く。Pandocの拡張markdownについては[Pandocユーザーズガイド](https://pandoc-doc-ja.readthedocs.io/ja/latest/users-guide.html)に詳しく書いてある。どんな見た目になってるかは、Markdown Preview Enhancedから確認。

```yaml
---
output:
  word_document:
    pandoc_args: ['--filter=pandoc-crossref',"--reference-doc=reference.docx"]
figureTitle: "図 "
tableTitle: "表 "
listingTitle: "コード "
figPrefix: "図."
eqnPrefix: "式."
tblPrefix: "表."
lstPrefix: "コード."
---
```

## Wordにする
reference.docxを編集中のmarkdownファイルと同じフォルダにコピー(それか、pandoc_argsに書いてるパスを絶対パスにする)して、「Markdown Preview Enhancedの表示画面をクリック->コンテキストメニューのPandocを選択」するとWordファイルが生成。表示がちょっとアレなところもあると思うので、微調整して完成。

## サンプル
[ここ](https://github.com/msntts/memorandum/tree/master/pandoc-setup/sample)にmarkdown(Pandoc拡張)とwordスタイルテンプレートのサンプルを置きました。  

# よくわからなかったこと
- Wordのスタイルテンプレートって、リスト表示(見出しのレベルに応じて1.とか1.1を付ける)の設定は保存されない?
- 図表の参照の仕方はわかったけど、章節の参照の方法がよくわからなかった
- 相互参照の設定はされないっぽい