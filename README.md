# hugo-sandbox
[Hugo](https://gohugo.io/)でサイトを作るときのテンプレ的なもの

## 実行手順

```bash
git clone https://github.com/youichiro/hugo-sandbox.git
cd hugo-sandbox
hugo server
```

## トップページスクショ

![hoge](https://user-images.githubusercontent.com/20487308/90981347-67a85c80-e59b-11ea-915f-12b1ba571bf5.png)


## 構成とコメント

```
archetypes/
    default.md                      // hugo new したときの記事の雛形
assets/
    scss/
        block/                      // 各ブロックのscss
            ...
        config/
            _base.scss              // 全体に適用されるべきスタイルを書く
            _colors.scss            // フォントカラーを定義する
            _fonts.scss             // フォントに関するクラスを書く
            _mixins.scss            // mixinを定義する
            _reset.scss             // ブラウザのデフォルトスタイルを打ち消す
        main.scss                   // scssを集約する headでこれを指定している
content/
    about/
        _index.md                   // セクションテンプレート(layouts/section/about.html)を使用
    news/
        _index.md                   // リストテンプレート(layouts/news/list.html)を使用
        20200823_first_news.md      // シングルページテンプレート(layouts/news/single.html)を使用
        20200823_second_news.md     // 〃
layouts/
    _default/
        baseof.html                 // ベーステンプレート 全ページ共通のHTML構造
    partials/
        footer.html                 // フッターを書く
        head.html                   // headタグの中身を書く
        header.html                 // ヘッダーを書く
        icon.html                   // アイコンを表示するパーツ
    index.html                      // ホームページテンプレート トップページそのもの
    section/                        // セクションテンプレートはこの中にセクション名のhtmlファイルを作成する
        about.html                  // aboutセクションのセクションテンプレート
    news/
        list.html                   // newsセクションのリストテンプレート
        single.html                 // newsセクションのシングルページテンプレート
static/
    images/                         // 使用する画像はここに配置する
        favicon.jpeg
        icon.jpg
config.toml                         // サイトの全般的な設定 .Site変数で参照できる
```


## トップページを編集する

- トップページは`layouts/index.html`に書く
- `{{ define "main" }}` `{{ end }}` で囲むことで `layouts/_default/baseof.html` のベーステンプレートに当てはめる
- htmlを部分的に別ファイルで分割したいときは `layouts/partials/`に新たにhtmlファイルを作成する(パーシャルと呼ぶ)
- `{{- partial "パーシャル名" }}` でそのパーシャルの内容を表示することができる

```html
{{ define "main" }}
  <!-- ここにコンテンツを書く -->
{{ end }}
```

```html
<!-- ここにパーシャルを当てはめる -->
{{- partial "xxx" }}
```

## 固定ページを作成する

Aboutページを新たに作成したい場合

- `hugo new about/_index.md`を実行する
  - `content/about/_index.md`が生成される
  - 表示させたい文章などのコンテンツはこのファイルに書く
- `layouts/secsion/about.html`を作成する
  - これがAboutページのセクションテンプレートとなる
  - `http://localhost:1313/about`が表示できるようになる
  - このファイルでAboutページの構成を作成する
  - `{{ define "main" }}` `{{ end }}` で囲むことで共通のHTML構造である`layouts/_default/baseof.html`のmainブロックにはめ込んでいる
  - `{{ .Title }}`でmdファイルのtitleを参照できる
  - `{{ .Content }}`でmdファイルの中身を参照できる


## コンテンツ一覧ページを作成する

ニュース記事のように逐次的にコンテンツを追加・更新するような場合、それらを自動で一覧表示するページが必要になる

ニュース記事ページとニュース一覧ページを表示する手順：

- ニュース記事ページの作成
  - `hugo new news/sample.md`で記事を書くmdファイルを作成する
  - これはシングルページテンプレートを使って表示する
  - `layouts/news`ディレクトリを作成し、更に`single.html`を作成する
  - 固定ページと同様に`{{ .Title }}`や`{{ .Content }}`でmdファイルの内容を表示する
  - `http://localhost:1313/news/sample`が表示できるようになる
- ニュース一覧ページの作成
  - `hugo new news/_index.md`でセクションページのmdファイルを作成する
  - 記事のリストを表示したいので、リストテンプレートを使って表示する
  - `layouts/news/list.html`を作成する
  - ```
    <ul>
      {{ range .RegularPages }}
      <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
    </ul>
    ```
    これで今開いているセクションに属するページの一覧を表示してくれる


## リンクを貼る

`<a href="/about">about</a>`でAboutページへのリンクを貼ることができる


## 画像を表示する

- `static/images`に画像ファイル(icon.jpg)を配置する
- `<img class="icon" src="/images/icon.jpg">`でその画像を表示できる
  - このsrcのパスはpublic内でのパスを指定している


## セクションページ一覧を表示する

ヘッダーにメニューとしてセクションページを一覧表示するような場合、セクションページを自動で取得できる

```
<ul>
  {{ range .Site.Sections }}
    <li>
      <a href="{{ .RelPermalink }}">{{ .Title }}</a>
    </li>
  {{ end }}
</ul>
```

セクションページのmdファイルの`weight`で並べる順番を指定することができる [参考](https://gohugo.io/templates/lists/#order-content)


## 変数を定義する

独自の変数を定義したいときは`config.toml`に`params`セクションを用意し、その下に変数を定義する

変数の定義：

```toml
baseURL = "http://localhost:1313"
languageCode = "ja"
title = "hugo-sandbox"

[params]
  GithubPage = "https://github.com/youichiro/hugo-sandbox"
```

変数の参照：

```html
<a href="{{ .Site.Params.GithubPage }}">source code</p>
```

## ビルドする

- `hugo`コマンドを実行すると、`public`ディレクトリにサイト表示に必要なファイルを出力する
- 例えば`public/index.html`を見ると、パーシャルやブロックの中身が全部統合されていることがわかる
- このディレクトリをサーバに配置することでサイトを公開することができる



## Hugo参考サイト

- [まくまくHugo/Goノート](https://maku77.github.io/hugo/)
