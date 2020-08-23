# hugo-sandbox
[Hugo](https://gohugo.io/)でサイトを作るときのテンプレ的なもの

## 実行手順

```bash
git clone https://github.com/youichiro/hugo-sandbox.git
cd hugo-sandbox
hugo server
```

## トップページスクショ

![hoge](https://user-images.githubusercontent.com/20487308/90984755-0475f480-e5b2-11ea-9509-c43ffa9bb3d1.png)


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
        news_list_summary.html      // ニュース記事を新着5件表示するパーツ
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

```html
{{ define "main" }}
  <!-- ここにコンテンツを書く -->
{{ end }}
```

## パーシャルを作成・表示する

- htmlを部分的に別ファイルで分割したいときは `layouts/partials/`に新たにhtmlファイルを作成する(パーシャルと呼ぶ)
- `{{- partial "パーシャル名" . -}}` でそのパーシャルの内容を表示することができる

```html
<!-- ここにパーシャルを当てはめる -->
{{- partial "xxx" . -}}
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
  - ```html
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


## メニューを表示する

`config.toml`にメニューとして表示したいページの情報を記述する<br>
weightで並べる順番を指定している

```toml
[menu]
  [[menu.main]]
    name = "Home"
    url = "/"
    weight = 1

  [[menu.main]]
    name = "About"
    url = "/about"
    weight = 2

  [[menu.main]]
    name = "News"
    url = "/news"
    weight = 3
```

`.Site.Menus.main`で参照してメニューを作ることができる

```html
<ul>
  {{ range .Site.Menus.main }}
    <li>
      <a href="{{ .URL }}">{{ .Name }}</a>
    </li>
  {{ end }}
</ul>
```

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

## メディアクエリを適用する

`assets/scss/config/_mixin.scss`にメディアクエリを簡単に利用するためのmixinを用意しました<br>
デバイスの横幅に応じて4つのbreakpoint(sm, md, lg, xl)を定義しています<br>
例えばmdサイズ以下のときにスタイルを適用したい場合は以下のように書きます

```scss
html {
  font-size: 100%;

  @include mq-down(md) {
    font-size: 80%;
  }
}
```


## 最新のコンテンツ一覧を表示する

`.Site.RegularPages`でサイト内の全てのシングルページを取得することができます<br>
次のコードはシングルページを作成日順に新着5件表示するものです

```html
<ul>
  {{ range first 5 .Site.RegularPages.ByDate.Reverse }}
  <li>
    <a href="{{ .RelPermalink }}">{{ .Title }}</a>
  </li>
  {{ end }}
</ul>
```


## ビルドする

- `hugo`コマンドを実行すると、`public`ディレクトリにサイト表示に必要なファイルを出力する
- 例えば`public/index.html`を見ると、パーシャルやブロックの中身が全部統合されていることがわかる
- このディレクトリをサーバに配置することでサイトを公開することができる



## Hugo参考サイト

- [まくまくHugo/Goノート](https://maku77.github.io/hugo/)
- [Yuuniworks Notes](https://note.yuuniworks.com/study/hugo.html)


## TODO

- [ ] FontAwesomeを使う
- [ ] OGPの設定
