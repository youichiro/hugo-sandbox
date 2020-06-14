## hugo-sandbox

### 実行

```bash
git clone https://github.com/youichiro/hugo-sandbox.git
cd hugo-sandbox
hugo server
```

### 構成とコメント

```
archetypes/
  default.md        // hugo new したときの記事の雛形
assets/
  scss/
    block/          // 各ブロックのscss
      ...
    config/
      _base.scss    // 全体に適用されるべきスタイルを書く
      _colors.scss  // フォントカラーを定義する
      _fonts.scss   // フォントに関するクラスを書く
      _reset.scss   // ブラウザのデフォルトスタイルを打ち消す
    main.scss       // scssを集約する, headでこれを指定している
content/
  about/            // aboutセクション
    _index.html     // セクションテンプレートが使用される
layouts/
  _default/
    baseof.html     // HTMLの共通部分, ベーステンプレート
    index.html
    list/html
  partials/
    footer.html     // フッターを書く
    head.html       // headタグの中身を書く
    header.html     // ヘッダーを書く
    icon.html       // partialにしておくと使い回せる
  section/
    about.html      // aboutのセクションテンプレート
  index.html        // ホームテンプレート, トップページそのものとする
static/
  images/           // 使用する画像をここに配置する
    favicon.jpeg
    icon.jpg
config.toml         // サイトの全般的な設定, .Site変数で参照できる
```


## トップページの作成

トップページは`layouts/index.html`に書く
`{{ define "main" }}` `{{ end }}` で囲むことで `layouts/_default/baseof.html` のベーステンプレートに当てはめる
htmlを別ファイルで分割したいときは `layouts/partials/xxx.html`にそのhtmlを作成し、`{{- partial "xxx" }}` で読み込む


## 固定ページの作成

accessページを新たに作成したい場合、まず `hugo new access/_index.html`を実行する
`content/access/_index.html`が作成されるが、これは特に編集しない
`layouts/secsion/access.html`を作成する
これがaccessページのセクションテンプレートとなる
ここにコンテンツを書く
`{{ define "main" }}` `{{ end }}` で囲むことを忘れないように


## リンクを書く
`<a href="{{ .Site.BaseURL }}/about">about</a>` で aboutページにリンクする
`.Site`は`config.toml`に定義された変数を参照することができ、`.Site.BaseURL`でその値を取得している



