# hugo-sandbox
[Hugo](https://gohugo.io/)でサイトを作るときのテンプレ的なもの

## 実行

```bash
git clone https://github.com/youichiro/hugo-sandbox.git
cd hugo-sandbox
hugo server
```

![hoge](https://user-images.githubusercontent.com/20487308/84595709-56a0e800-ae94-11ea-9c32-1344f7759ad9.png)


## 構成とコメント

```
archetypes/
    default.md                // hugo new したときの記事の雛形
assets/
    scss/
        block/                // 各ブロックのscss
            ...
        config/
            _base.scss        // 全体に適用されるべきスタイルを書く
            _colors.scss      // フォントカラーを定義する
            _fonts.scss       // フォントに関するクラスを書く
            _reset.scss       // ブラウザのデフォルトスタイルを打ち消す
        main.scss             // scssを集約する, headでこれを指定している
content/
    about/                    // aboutセクション
        _index.html           // セクションテンプレートが使用される
layouts/
    _default/
        baseof.html           // HTMLの共通部分, ベーステンプレート
        index.html
        list/html
    partials/
        footer.html           // フッターを書く
        head.html             // headタグの中身を書く
        header.html           // ヘッダーを書く
        icon.html             // partialにしておくと使い回せる
    section/
        about.html            // aboutのセクションテンプレート
    index.html                // ホームテンプレート, トップページそのものとする
static/
    images/                   // 使用する画像をここに配置する
        favicon.jpeg
        icon.jpg
config.toml                   // サイトの全般的な設定, .Site変数で参照できる
```


## トップページの編集

- トップページは`layouts/index.html`に書く
- `{{ define "main" }}` `{{ end }}` で囲むことで `layouts/_default/baseof.html` のベーステンプレートに当てはめる
- htmlを部分的に別ファイルで分割したいときは `layouts/partials/`に新たにhtmlファイルを作成し、`{{- partial "xxx" }}` でそのパーシャルを読み込む

```html
{{ define "main" }}
  <!-- ここにコンテンツを書く -->
{{ end }}
```

```html
<!-- ここにパーシャルを当てはめる -->
{{- partial "xxx" }}
```

## 固定ページの作成

例えばアクセスページを新たに作成したい場合

- `hugo new access/_index.html`を実行する
  - `content/access/_index.html`が生成される
  - `draft: false`に変更しておく
- `layouts/secsion/access.html`を作成する
  - これがアクセスページのセクションテンプレートとなる
  - このファイルでアクセスページを制作していく
  - `{{ define "main" }}` `{{ end }}` で囲むことを忘れないように


## リンクを書く

- `<a href="{{ .Site.BaseURL }}/about">about</a>`でAboutページへのリンクになる
- `.Site`で`config.toml`に定義された変数を参照することができ、`.Site.BaseURL`でその値を取得している


## 変数を定義する

- 独自の変数を定義したいときは`config.toml`に`params`セクションを用意し、その下に変数を定義する

変数の定義：

```toml
baseURL = "http://localhost:1313"
languageCode = "ja"
title = "hugo-sandbox"

[params]
  Twitter = "@cinnamon_416"
```

変数の参照：

```html
<p>{{ .Site.Params.Twitter }}</p>
```

## ビルドする

- `hugo`コマンドを実行すると、`public`ディレクトリにサイトに必要なファイルを出力してくれる
- 例えば`public/index.html`を見ると、パーシャルやブロックの中身が全部統合されていることがわかる
- このディレクトリをサーバに配置することでサイトを公開することができる
