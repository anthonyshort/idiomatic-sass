# イディオマティックな Sass を書くための原則

多くの人が Sass を CSS と同じようなものだと考えていますが、できるかぎり最高のコードを書けるようになりたいと思うなら、 Sass には取り組むべき固有のチャレンジがたくさんあります。このドキュメントを通して、他の人とのコードの共有やよくある落とし穴の回避に役に立つコーディングスタイルを見ていきます。

これらの原則に加えて [Idiomatic CSS](https://github.com/necolas/idiomatic-css) を読み、標準的な CSS のベストプラクティスとあわせてこれらを利用しましょう。すぐれた CSS の書く方法を理解することは、このドキュメントの礎となっています。そのためこの原則には他の原則と重複する部分があります。

## 目次

1. オブジェクト指向 Sass  Object-Oriented Sass
2. 命名規約  Naming Conventions
3. セレクタ  Selectors
4. プロパティ  Properties
   * 順序  Ordering
5. ネスト  Nesting
6. インデント  Indentation
7. ファイル構造  File Structure
8. 関数  Functions
9. ミックスイン  Mixins
10. モジュール  Modules
   * ファイル構造  File Structure
   * 名前空間  Namespacing
   * モジュールエントリポイント  Module Entry Point
   * パッケージマネジメント  Package Management
   * ロードパス  Load Paths
   * 依存関係  Dependencies
11. 定義  Definitions

## オブジェクト指向 Sass  Object-Oriented Sass

よい Sass コードを書くことはオブジェクトを正しく分割しモジュラー化することから始まります。これはまちがいなく CSS を書く上での他のどの側面よりも重要なポイントです。

よく書かれた Sass の特徴:

### 分離されている  Decoupled

オブジェクトは他のオブジェクトを操作するべきではありません。例: `.message` はネストされた `.list` というオブジェクトを変更してはなりません。代わりに `.message__list` といった子セレクタを使い、 `<div class="list message__list">` あるいは `<div class="message"><div class="list list--small"></div></div>` といった感じでマークアップに両方のクラスを使用しましょう。

### 各オブジェクトの機能が固有である  Specific

機能を分割して小さなオブジェクトにしてください。各オブジェクトはただひとつのことをうまくやるようにすべきです。

### 場所に依存しない  Not location-based

配置場所に依存したスタイルイングを使うのは絶対にやめましょう。これは、ブロックが他のブロックの中にあるという理由で異なるスタイリングにはしないという意味です。オブジェクトには `#sidebar .block {}` といった場所に関連したスタイルを持たせるのではなく `.block--large {}` というように「モディファイア」を持たせるようにしましょう。

### ID を使わない  Never uses IDs

そう、 ID は絶対に使いません。 ID を使う必要はないですし ID はその性質から言って再利用ができません。

### レイアウトをスタイルから分ける。  Separates layout from style.

これは、 background や border を扱うオブジェクトは padding や margin を制御しないという意味です。
スタイルは一般にいくつかのカテゴリに分けられます: レイアウト、テクスチャ、タイポグラフィ。
各オブジェクトは一般にこれらのうちひとつだけを扱います。
ただしこの点についてはプラグマティックでいましょう。
常に「再利用」ということを考えましょう。

次のセクションの命名規約のうちひとつを使用して、これらのルールを強制しましょう。

## 命名規約  Naming Conventions

セレクタに対して一貫した命名規約を使用することが重要です。
この点については [BEM](http://bem.info) や [Montage](http://montagejs.org/docs/Naming-Conventions.html) を読むことをおすすめします。

### BEM

```scss
.block-name {}
.block-name__child-name {}
.block-name--modifier {}
```

### Montage

```scss
.namespace-BlockName {}
.namespace-BlockName-childName {}
.namespace-BlockName--modifier {}
```

最も重要なことは、チームとして 1 つを選択しそれを守ることです。

## セレクタ  Selectors

* 複数のセレクタがあるルールセットでは 1 行に 1 つのセレクタのみを置きましょう。
* セレクタ内の属性値には引用符を付けましょう。例: `input[type="checkbox"]` 。
* ルールセットの閉じかっこはルールセットの最初の文字と同じ列に配置しましょう。
* 各ルールセットは空行で分けましょう。
* ルールセットの開きかっこの前にはスペースをひとつ入れましょう。

## プロパティ  Properties

* 宣言ブロック内では 1 行に 1 つの宣言を書きましょう。
* 各宣言には 1 段階のインデントを使いましょう。
* 小文字の省略形の 16 進数値を使いましょう。例: `#aaa` 。
* シングルクォートとダブルクォートは一貫性を持って使いましょう。よいのはダブルクォートです。例: `content: ""` 。
* それが可能なところでは、 0 を示す値の単位は指定しないようにしましょう。例: `margin: 0` 。
* 宣言ブロック内の最後の宣言の末尾にはセミコロンをつけましょう。
* コンマで分割されたプロパティや関数の値のところではコンマの後にスペースをひとつ入れましょう。

### 順序  Ordering

1. `$varaible` は **必ず** いちばん上に書きましょう。
2. `@extend` は必ずプロパティの前に書きましょう。 Ruby でクラスを extend するときと同じです。
3. `@include` は 2 番目に書きましょう。こうすることで、プロパティがミックスインをオーバーライドすることができます。
4. プロパティはその後に書きましょう。オプションで、そのタイプでグルーピングしたりアルファベット順に並べたりしましょう。
5. コンテンツブロックを持つミックスインはその後に書きましょう。 `@include someMixin { properties }` 。
6. その次が自身を指すセレクタ。 `$.modifier` 。
7. 子セレクタは最後に書きましょう。

経験則による基本は「アットマーク始まりのルール、プロパティ、その後にブロック」です。

きれいに整形されたセレクタの例:

```scss
.selector-1,
.selector-2,
.selector-3[type="text"]  {
  $bg: blue;
  $fallback: green;

  @extend .clearfix;
  @include border-box;

  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  display: block;
  font-family: helvetica, arial, sans-serif;
  color: #333;
  background: #fff;
  background: linear-gradient(#fff, rgba(0, 0, 0, 0.8));

  @include after {
    position: absolute;
  }

  &.selector--modifier {
    background: red;
  }

  .selector__child {
    display: none;
  }
}
```

## ネスト  Nesting

* 深さが 2 を越えるネストはやめましょう。階層が深くなるのはセレクタの詳細度が高すぎる悪い CSS のサインです。
* セレクタ内のルールセットは空行 1 つで分けましょう。他のセレクタのルールにも従うようにしましょう。

## インデント  Indentation

* インデントは空白 2 つにしましょう。

## ファイル構造  File Structure

* 論理的なコードのモジュールは、別々のファイルに入れましょう。複数のオブジェクトを同じファイルに入れるのはやめましょう。こうすることで、コメントブロックに頼らずにファイルシステムを使って Sass を扱うことができます。
* ミックスイン/プレースホルダ/関数はできるかぎり個別のファイルに入れましょう。
* ファイル名はそこに含まれるコンポーネントに対してつけるようにしましょう。 `block-list` オブジェクトは `block-list.scss` ファイルに入っているべきです。

## 関数  Functions

* 関数にはダッシュ（ `-` ）と名前空間のプレフィックスをつけましょう: `-rg-columns` 。
* プライベート関数の場合、名前空間は省略可能です: `columns` 。
* 関数には DocBlock やそれに似たものでドキュメントをつけましょう。

## ミックスイン  Mixins

* ミックスインはダイナミックなプロパティがある場合にのみ使用しましょう。そうでない場合は `@extend` を使いましょう。
* セレクタを出力するミックスインは大文字始まりのキャピタルケースにしましょう: `@mixin GridBuilder` 。
* プロパティだけを出力するミックスインは小文字始まりのキャメルケースにしましょう: `@mixin borderBox` 。
* パブリックモジュールの一部であるミックスインにはプレフィックスをつけましょう: `@mixin as-GridBuilder` 。
* 一般的に、ロジックを含むミックスインはその他のプログラミング言語のように 50 行程度よりも長くならないようにしましょう。
* 現在のファイルの外側で使われることのないプライベートのミックスインは先頭にダッシュをつけましょう: `@mixin -gridHelper` 。
* 4 個を超えるパラメータを使うのはやめましょう。 4 個を超える場合は複雑すぎることを表すサインです。ハッシュを使うと SASS がよりかんたんになります。
* ミックスインにはドキュメントをつけましょう。

```scss
// 各ブレークポイントに対してループを回し、ブレークポイントミックスインを使用してクラスを構築する。
// 最初のブレークポイントはモバイルファーストでメディアクエリなしとなる。
//
// @param {List} $breakpoints カラムのブレークポイントのリスト
// @param {Boolean} $spacing クラスの間にスペースを含むか？
// @param {Boolean} $visibility 可視性のクラスを含めるか？
// @param {Boolean} $layout レイアウトのクラスを含めるか？
// @api private
@mixin -rg-Breakpoints($breakpoints, $spacing: true, $visibility: true, $layout: true) {
  @each $columns in $breakpoints {
    @if index($breakpoints, $columns) == 1 {
      @include -rg-BreakpointClasses($columns, $spacing, $visibility, $layout);
    }
    @else {
      @include rg-from($columns) {
        @include -rg-BreakpointClasses($columns, $spacing, $visibility, $layout);
      }
    }
  }
}
```

## モジュール/パッケージ  Modules/Packages

Sass コードの共有はますます重要になっています。 Sass の適切なモジュールシステムを使用しない場合、コードの共有が一貫性のあるものとなり挙動が予測可能なものになるようにいくつかのルールを作る必要があります。
Sass パッケージは Bower と GitHub でサッと使えますが、 Sass パッケージの実装方法には一貫性がありません。

いくつかの全般的なルール:

* すべてのモジュールは名前空間を *持たなければならない* 。
* プライベートの関数とミックスインには先頭にダッシュをつける: `@mixin -rg-gridUnit` 。
* モジュールのインポートではセレクタを生成しない。
* ミックスイン/プレースホルダ/関数は `@import "rg-Grid/mixins"` と個別にインポートできるべきである。
* グローバル変数に頼るのは避ける。
* できるかぎりプレースホルダセレクタを使う。
* グローバルな変数には必ずすべて名前空間をつける。

### ファイル構造  File Structure

* インストールされたサードパーティのモジュールは必ず `components` ディレクトリ内に置きましょう。
* ローカルなモジュールは `compoents` ディレクトリの隣の `local` ディレクトリに置きましょう。
* 画像、フォントその他のアセットはすべて `assets` ディレクトリに入れましょう。

構造の例:

```
/module-name
  /assets
    /fonts
    /images
  /components
    /responsive-grid
    /clearfix
    /animation
  /local
    /homepage
  /lib
    /mixins
    /functions
  bower.json
  index.scss
```

### 名前空間  Namespacing

* インポートされたセレクタ、プレースホルダ、関数にはすべて名前空間をつけましょう。
* 名前空間は短くし（ 2-5 文字）、末尾にダッシュをつけましょう: `rg-Grid` 。

### モジュールエントリポイント  Module Entry Point

* 各モジュールはエントリポイントとして `index.scss` ファイルをもたせましょう: `@import "module-name/index"` 。
* 各モジュールは個別にコンパイルして使用できるべきなので、エントリポイントのファイルはファイル名にアンダースコアをつける必要はありません。
* このエントリポイントのファイルをインポートしても何も出力が出ないようにしましょう。
* **そのモジュールの名前のエントリポイントのミックスイン** を作りましょう。例: `rg-Grid` モジュールは `rg-Grid` ミックスインを持つ。

### パッケージマネジメント  Package Management

* 現時点でのおすすめの Sass のパッケージマネージャは Bower です。
* できるかぎり Bower にパッケージを登録するのはやめましょう。かわりに GitHub のショートハンドシンタックス `user/project` を使いましょう。例: `fonzie/responsive-grid` 。
* すべてのパッケージは `components` ディレクトリにインストールします。

### ロードパス  Load Paths

* `components` ディレクトリがロードパスに追加されているという前提にします。

### 依存関係  Dependencies

モジュールがインポートされただけでは何も出力しないことになっているので、ファイルがすでにインポートされているかもしれないという不安なしにパッケージは他のパッケージを安全にインポートできます。このため、他からの依存のために読みこまれたパッケージは自分自身が依存パッケージを安全に require できます。

例えば、 `Grid` パッケージは `Clearfix` パッケージに依存しますが、 `LayoutHelpers` も `Clearfix` パッケージに依存するものとします。
これらのパッケージはいずれも `.clearfix` が 2 回出力されるという不安なく `@import "clearfix/index"` ができます。

`components` ディレクトリがロードパスに追加されているという前提に立っているので、パッケージは依存した他のパッケージをかんたんに require することができます。

## 定義  Definitions

#### オブジェクト  Object

デザインのひとつのパーツ。通常かなり小さいもの。オブジェクトは `.message` や `.block` 、 `.list` 、 `.post` のようなものになります。
オブジェクトは独立していなくてはなりません。レゴブロックのようなものだと考えてください。オブジェクトは「モディファイア」と「子」を持ちます。

#### 子要素  Children

「オブジェクト」が親の場合、そのオブジェクトの下に位置するパーツはその子どもとみなします。子要素はそれが所属するオブジェクトからのみ制御されます。 `.message` オブジェクトはスタイルを指定したタイトル `.message__title` を持つことができ、このタイトルは子要素と呼びます。

#### モジュール  Module

CSS 、ミックスイン、関数とアセット（画像やフォントなど）からなるひとつの機能単位。
モジュールはひとつエントリポイントとひとつの目的と役割を持ちます。例: グリッドフレームワークはモジュールになるでしょう。

#### パッケージ  Package

Bower などのパッケージマネージャを通して他の人と共有されるモジュールは、一般にパッケージと呼ばれます。
「モジュール」と「パッケージ」ということばはかなり似ています。

#### ブロック  Block

ブロックは「オブジェクト」というコンセプトに対する別の用語です。

#### エレメント  Element

「オブジェクト」と「ブロック」についていう場合、「エレメント」という単語は「子」という単語に置き換えることができます。

#### モディファイア  Modifier

「オブジェクト」はスタイルを少しだけ変更する形で修飾することができます。これはテーマや別のスタイルと捉えてください。例えば、 `.list` オブジェクトはテキストを小さくする `.list--small` モディファイアを持つことができます。
