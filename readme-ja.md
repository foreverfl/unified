# [![unified][logo]][site]

[![Build][build-badge]][build]
[![Coverage][coverage-badge]][coverage]
[![Downloads][downloads-badge]][downloads]
[![Size][size-badge]][size]
[![Sponsors][sponsors-badge]][collective]
[![Backers][backers-badge]][collective]
[![Chat][chat-badge]][chat]

**unified**はプラグインを使用してコンテンツを検査および変換することができます。

## 目次

* [これは何ですか？](#what-is-this)
* [いつ使うべきですか？](#when-should-i-use-this)
* [インストール](#install)
* [使用方法](#use)
* [概要](#overview)
* [API](#api)
  * [`processor()`](#processor)
  * [`processor.compiler`](#processorcompiler)
  * [`processor.data([key[, value]])`](#processordatakey-value)
  * [`processor.freeze()`](#processorfreeze)
  * [`processor.parse(file)`](#processorparsefile)
  * [`processor.parser`](#processorparser)
  * [`processor.process(file[, done])`](#processorprocessfile-done)
  * [`processor.processSync(file)`](#processorprocesssyncfile)
  * [`processor.run(tree[, file][, done])`](#processorruntree-file-done)
  * [`processor.runSync(tree[, file])`](#processorrunsynctree-file)
  * [`processor.stringify(tree[, file])`](#processorstringifytree-file)
  * [`processor.use(plugin[, options])`](#processoruseplugin-options)
  * [`CompileResultMap`](#compileresultmap)
  * [`CompileResults`](#compileresults)
  * [`Compiler`](#compiler)
  * [`Data`](#data)
  * [`Parser`](#parser)
  * [`Pluggable`](#pluggable)
  * [`PluggableList`](#pluggablelist)
  * [`Plugin`](#plugin)
  * [`PluginTuple`](#plugintuple)
  * [`Preset`](#preset)
  * [`ProcessCallback`](#processcallback)
  * [`Processor`](#processor-1)
  * [`RunCallback`](#runcallback)
  * [`Settings`](#settings)
  * [`TransformCallback`](#transformcallback)
  * [`Transformer`](#transformer)
* [タイプ](#types)
* [互換性](#compatibility)
* [貢献](#contribute)
* [スポンサー](#sponsor)
* [謝辞](#acknowledgments)
* [ライセンス](#license)

## これは何ですか？

unifiedは2つのものです：

* **unified**は、構造化されたデータ（AST）としてコンテンツを扱う500以上の無料でオープンソースのパッケージの集合体です
* `unified`（このプロジェクト）は、GitHubで130万以上のプロジェクトで使用されている、プラグインでコンテンツを処理するためのコアパッケージです

さまざまな種類のコンテンツを扱うためのいくつかのエコシステムがunifiedを基に構築されています。
特に、[remark][]（マークダウン）、[rehype][]（HTML）、[retext][]（自然言語）があります。
これらのエコシステムは互いに接続することができます。

* 私たちについての詳細は、[`unifiedjs.com`][site]をご覧ください
* 最新情報については、Twitterの[@unifiedjs][twitter]をフォローしてください
* 質問がある場合は、[support][]をご覧ください
* 協力したい場合は、以下の[contribute][]と[sponsor][]をご覧ください

## いつ使うべきですか？

場合によっては、すでにunifiedを使用している可能性があります。
例えば、MDX、Gatsby、Docusaurusなどで使用されています。
そのような場合、`unified`を自分で追加する必要はありませんが、それらのプロジェクトにプラグインを含めることができます。

しかし、本当の楽しみ（一部の人にとっては）は、自分の手を汚して構文ツリーを扱い、自分でそれを使って構築することです。
それらのプロジェクトを作成したり、Prettierのようなものを作ったり、独自のサイトジェネレーターを作ったりすることができます。
ユーティリティを接続して、問題をチェックしたり、一つのものから別のものに変換したりする独自のプラグインを作成することができます。

一種類のコンテンツ（マークダウンなど）を扱う場合は、そのエコシステムのメインパッケージを代わりに使用できます（つまり`remark`）。
異なる種類のコンテンツ（マークダウンとHTMLなど）を扱う場合は、`unified`自体を使用し、必要なプラグインを選択することをお勧めします。

## インストール

このパッケージは[ESMのみ][esm]です。
Node.js（バージョン16以上）では、[npm][]でインストールしてください：

```sh
npm install unified
```

Denoでは[`esm.sh`][esmsh]を使用します：

```js
import {unified} from 'https://esm.sh/unified@11'
```

ブラウザでは[`esm.sh`][esmsh]を使用します：

```html
<script type="module">
  import {unified} from 'https://esm.sh/unified@11?bundle'
</script>
```

## 使用方法

```js
import rehypeDocument from 'rehype-document'
import rehypeFormat from 'rehype-format'
import rehypeStringify from 'rehype-stringify'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import {unified} from 'unified'
import {reporter} from 'vfile-reporter'

const file = await unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(rehypeDocument, {title: '👋🌍'})
  .use(rehypeFormat)
  .use(rehypeStringify)
  .process('# Hello world!')

console.error(reporter(file))
console.log(String(file))
```

結果：

```txt
問題は見つかりませんでした
```

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>👋🌍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
    <h1>Hello world!</h1>
  </body>
</html>
```

<!-- 古い名前： -->

<a name="description"></a>

## 概要

`unified`は構文ツリーを使用してコンテンツを処理するためのインターフェースです。
構文ツリーはプログラムが理解できるコンテンツの表現です。
これらのプログラムは*[プラグイン][api-plugin]*と呼ばれ、これらのツリーを取得して検査および変更します。
テキストから構文ツリーを得るために*[パーサー][api-parser]*があります。
そこからテキストに戻すために*[コンパイラ][api-compiler]*があります。
これが*プロセッサ*の*[プロセス][api-process]*です。

```ascii
| ........................ プロセス ........................... |
| .......... 解析 ... | ... 実行 ... | ... 文字列化 ..........|

          +--------+                     +----------+
入力 ->- | パーサー | ->- 構文ツリー ->- | コンパイラ | ->- 出力
          +--------+          |          +----------+
                              X
                              |
                       +--------------+
                       | トランスフォーマー |
                       +--------------+
```

###### プロセッサ

プロセッサはコンテンツを処理します。
単独では、`unified`（ルートプロセッサ）は機能しません。
機能するにはプラグインで構成される必要があります。
例えば：

```js
const processor = unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(rehypeDocument, {title: '👋🌍'})
  .use(rehypeFormat)
  .use(rehypeStringify)
```

そのプロセッサはさまざまなことができます。
以下のことができます：

* …マークダウンを解析する（`parse`）
* …解析されたマークダウンをHTMLに変換し、HTMLをフォーマットする（`run`）
* …HTMLをコンパイルする（`stringify`）
* …上記のすべてを行う（`process`）

すべてのプロセッサは別のプロセッサを実装します。
プロセッサを作成するには、別のプロセッサを呼び出します。
新しいプロセッサはその祖先と同じように動作するように構成されます。
しかし、子孫プロセッサが将来構成されても、祖先プロセッサには影響しません。

プロセッサがモジュールから公開されている場合（例えば、`unified`自体）、直接構成すべきではありません。それによってすべてのモジュールユーザーの動作が変更されてしまうからです。
これらのプロセッサは*[凍結][api-freeze]*されており、使用する前に呼び出して新しいプロセッサを作成する必要があります。

###### ファイル

文書を処理する際、その文書に関するメタデータが収集されます。
[`vfile`][vfile]は、unifiedとプラグインのためにファイルに関するデータ、メタデータ、およびメッセージを保存するファイル形式です。

これらのファイルを扱うためのいくつかの[ユーティリティ][vfile-utilities]があります。

###### 構文ツリー

unifiedで使用される構文ツリーは[unist][]ノードです。
ツリーは全体の文書を表し、各[ノード][node]は`type`フィールドを持つ単純なJavaScriptオブジェクトです。
ノードの意味と構文ツリーの形式は他のプロジェクトによって定義されています：

* [esast][] — JavaScript
* [hast][] — HTML
* [mdast][] — マークダウン
* [nlcst][] — 自然言語
* [xast][] — XML

前述の各プロジェクトにリストされ、[`syntax-tree`][syntax-tree]組織で維持されているツリーを扱うための多くのユーティリティがあります。
これらのユーティリティはunified自体よりも低いレベルであり、プラグインを作成するために使用できるビルディングブロックです。

<!-- 古い名前： -->

<a name="list-of-processors"></a>

###### エコシステム

各構文ツリーの周りには、その特定の種類のコンテンツに焦点を当てたエコシステムがあります。
その核心は、テキストをツリーに解析し、そのツリーをテキストに戻すコンパイルです。
また、エンドユーザーがそのツリーに関する知識を持っていなくても、構文ツリーで動作するプラグインも提供します。

* [rehype][] (hast) — HTML
* [remark][] (mdast) — マークダウン
* [retext][] (nlcst) — 自然言語

<a name="list-of-plugins"></a>

###### プラグイン

前述の各エコシステムには、あらゆる種類のことを行うために選択できる大規模なプラグインセットが付属しています。

* [remarkプラグインのリスト][remark-plugins] ·
  [`remarkjs/awesome-remark`][awesome-remark] ·
  [`remark-plugin` トピック][topic-remark-plugin]
* [rehypeプラグインのリスト][rehype-plugins] ·
  [`rehypejs/awesome-rehype`][awesome-rehype] ·
  [`rehype-plugin` トピック][topic-rehype-plugin]
* [retextプラグインのリスト][retext-plugins] ·
  [`retextjs/awesome-retext`][awesome-retext] ·
  [`retext-plugin` トピック][topic-retext-plugin]

また、任意のエコシステムで動作するいくつかのプラグインもあります：

* [`unified-diff`](https://github.com/unifiedjs/unified-diff)
  — GitHub ActionsとTravisで関連のないメッセージを無視する
* [`unified-infer-git-meta`](https://github.com/unifiedjs/unified-infer-git-meta)
  — Gitから文書のメタデータを推論する
* [`unified-message-control`](https://github.com/unifiedjs/unified-message-control)
  — コンテンツからのメッセージを有効化、無効化、および無視する

###### 設定

プロセッサは[プラグイン][api-plugin]または[`data`][api-data]メソッドで設定されます。
ほとんどのプラグインもオプションを通じて設定を受け入れます。
詳細については、各プラグインのreadmeを参照してください。

###### 統合

unifiedは[`unified-engine`][unified-engine]を通じてファイルシステムと統合できます。
CLIアプリは[`unified-args`][unified-args]で、Gulpプラグインは[`unified-engine-gulp`][unified-engine-gulp]で、言語サーバーは[`unified-language-server`][unified-language-server]で作成できます。
ストリーミングインターフェースは[`unified-stream`][unified-stream]で作成できます。

###### プログラミングインターフェース

`unified`が提供する[API][]は、複数のファイルを処理し、メタデータ（リントメッセージなど）にアクセスすることができます：

```js
import rehypeStringify from 'rehype-stringify'
import remarkParse from 'remark-parse'
import remarkPresetLintMarkdownStyleGuide from 'remark-preset-lint-markdown-style-guide'
import remarkRehype from 'remark-rehype'
import remarkRetext from 'remark-retext'
import retextEnglish from 'retext-english'
import retextEquality from 'retext-equality'
import {unified} from 'unified'
import {reporter} from 'vfile-reporter'

const file = await unified()
  .use(remarkParse)
  .use(remarkPresetLintMarkdownStyleGuide)
  .use(remarkRetext, unified().use(retextEnglish).use(retextEquality))
  .use(remarkRehype)
  .use(rehypeStringify)
  .process('*Emphasis* and _stress_, you guys!')

console.error(reporter(file))
console.log(String(file))
```

結果：

```txt
1:16-1:24 警告 強調にはマーカーとして `*` を使用すべきです                                 emphasis-marker remark-lint
1:30-1:34 警告 `guys` は不適切かもしれません。代わりに `people`、`persons`、`folks` を使用してください gals-man        retext-equality

⚠ 2つの警告
```

```html
<p><em>Emphasis</em> and <em>stress</em>, you guys!</p>
```

<!-- 古い名前： -->

<a name="processing-between-syntaxes"></a>

###### エコシステム間の変換

エコシステムは2つのモードで組み合わせることができます。

**ブリッジ**モードは、ツリーを1つの形式（*オリジン*）から別の形式（*デスティネーション*）に変換します。
異なるプロセッサがデスティネーションツリーで実行されます。
その後、元のプロセッサがオリジンツリーで続行します。

**ミューテート**モードも構文ツリーを1つの形式から別の形式に変換します。
しかし、元のプロセッサがデスティネーションツリーの変換を続行します。

前の例（"プログラミングインターフェース"）では、`remark-retext`はブリッジモードで使用されています：retextが完了した後、オリジンの構文ツリーが保持されます。一方、`remark-rehype`はミューテートモードで使用されています：新しい構文ツリーを設定し、オリジンツリーを破棄します。

以下のプラグインを使用してエコシステムを組み合わせることができます：

* [`remark-retext`][remark-retext] — マークダウンを自然言語に変換する
* [`remark-rehype`][remark-rehype] — マークダウンをHTMLに変換する
* [`rehype-retext`][rehype-retext] — HTMLを自然言語に変換する
* [`rehype-remark`][rehype-remark] — HTMLをマークダウンに変換する

## API

このパッケージは識別子`unified`（ルート`processor`）をエクスポートします。
デフォルトエクスポートはありません。

### `processor()`

新しいプロセッサを作成します。

###### 戻り値

新しい*[凍結されていない][api-freeze]*プロセッサ（[`processor`][api-processor]）。

このプロセッサはその祖先と同じように動作するように構成されています。
子孫プロセッサが将来構成されても、祖先プロセッサには影響しません。

###### 例

この例は、新しいプロセッサを作成し（`remark`から）、**stdin**(4)と**stdout**(4)にリンクする方法を示しています。

```js
import process from 'node:process'
import concatStream from 'concat-stream'
import {remark} from 'remark'

process.stdin.pipe(
  concatStream(function (buf) {
    process.stdout.write(String(remark().processSync(buf)))
  })
)
```

### `processor.compiler`

使用するコンパイラ（[`Compiler`][api-compiler]、オプション）。

### `processor.data([key[, value]])`

すべてのプラグインで利用可能な情報でプロセッサを構成します。
情報はオブジェクトに保存されます。

通常、オプションは特定のプラグインに与えることができますが、複数のプラグインで情報を共有することが意味をなす場合もあります。
例えば、すべての[フェーズ][overview]で必要な自己閉じのHTMLエレメントのリストがあります。

> 👉 **注**: 情報の設定は*[凍結された][api-freeze]*プロセッサでは行えません。
> まずプロセッサを呼び出して、新しい凍結されていないプロセッサを作成してください。

> 👉 **注**: TypeScriptでカスタムデータを登録するには、[`Data`][api-data]インターフェースを拡張してください。

###### 署名

* `processor = processor.data(key, value)`
* `processor = processor.data(dataset)`
* `value = processor.data(key)`
* `dataset = processor.data()`

###### パラメータ

* `key` ([`keyof Data`][api-data]、オプション) — 取得するフィールド
* `value` ([`Data[key]`][api-data]) — 設定する値
* `values` ([`Data`][api-data]) — 設定する値

###### 戻り値

設定時は現在のプロセッサ（[`processor`][api-processor]）、取得時は`key`の値（[`Data[key]`][api-data]）、またはキーなしで取得時は全データセット（[`Data`][api-data]）。

###### 例

この例は、情報を取得および設定する方法を示しています：

```js
import {unified} from 'unified'

const processor = unified().data('alpha', 'bravo')

processor.data('alpha') // => 'bravo'

processor.data() // => {alpha: 'bravo'}

processor.data({charlie: 'delta'})

processor.data() // => {charlie: 'delta'}
```

### `processor.freeze()`

プロセッサを凍結します。

凍結されたプロセッサは拡張されることを意図しており、直接構成されるべきではありません。

プロセッサが凍結されると、解凍することはできません。
同じように動作する新しいプロセッサは、プロセッサを呼び出すことで作成できます。

`.freeze()`を呼び出すことで、プロセッサを明示的に凍結することができます。
プロセッサは`.parse()`、`.run()`、`.runSync()`、`.stringify()`、`.process()`、または`.processSync()`が呼び出されたときに自動的に凍結されます。

###### 戻り値

現在のプロセッサ（[`processor`][api-processor]）。

###### 例

この例（`index.js`）は、`rehype`が自身への拡張を防ぐ方法を示しています：

```js
import rehypeParse from 'rehype-parse'
import rehypeStringify from 'rehype-stringify'
import {unified} from 'unified'

export const rehype = unified().use(rehypeParse).use(rehypeStringify).freeze()
```

そのプロセッサは次のように使用および構成できます：

```js
import {rehype} from 'rehype'
import rehypeFormat from 'rehype-format'
// …

rehype()
  .use(rehypeFormat)
  // …
```

似たような例が凍結されたインターフェースで動作しようとすると破損します。
この動作が許可されると予期しない動作が生じるため、エラーが発生します。
**これは無効です**：

```js
import {rehype} from 'rehype'
import rehypeFormat from 'rehype-format'
// …

rehype
  .use(rehypeFormat)
  // …
```

結果：

```txt
~/node_modules/unified/index.js:426
    throw new Error(
    ^

Error: 凍結されたプロセッサで `use` を呼び出すことはできません。
まず新しいプロセッサを作成してください。呼び出して: `processor()` の代わりに `processor` を使用してください。
    at assertUnfrozen (~/node_modules/unified/index.js:426:11)
    at Function.use (~/node_modules/unified/index.js:165:5)
    …
```

### `processor.parse(file)`

テキストを構文ツリーに解析します。

> 👉 **注**: `parse`はプロセッサがまだ*[凍結][api-freeze]*されていない場合、プロセッサを凍結します。

> 👉 **注**: `parse`は[解析フェーズ][overview]を実行し、実行フェーズや他のフェーズは実行しません。

###### パラメータ

* `file` ([`Compatible`][vfile-compatible]) — 解析するファイル。通常は`string`または[`VFile`][vfile]。`new VFile(x)`の`x`として受け入れられる任意の値

###### 戻り値

`file`を表す構文ツリー（[`Node`][node]）。

###### 例

この例は、`parse`を使用してファイルからツリーを作成する方法を示しています。

```js
import remarkParse from 'remark-parse'
import {unified} from 'unified'

const tree = unified().use(remarkParse).parse('# Hello world!')

console.log(tree)
```

結果：

```js
{
  type: 'root',
  children: [
    {type: 'heading', depth: 1, children: [Array], position: [Object]}
  ],
  position: {
    start: {line: 1, column: 1, offset: 0},
    end: {line: 1, column: 15, offset: 14}
  }
}
```

### `processor.parser`

使用するパーサー（[`Parser`][api-parser]、オプション）。

### `processor.process(file[, done])`

プロセッサで構成されたとおりに指定されたファイルを処理します。

> 👉 **注**: `process`はプロセッサがまだ*[凍結][api-freeze]*されていない場合、プロセッサを凍結します。

> 👉 **注**: `process`は[解析、実行、および文字列化フェーズ][overview]を実行します。

###### 署名

* `processor.process(file, done)`
* `Promise<VFile> = processor.process(file?)`

###### パラメータ

* `file` ([`Compatible`][vfile-compatible]、オプション) — ファイル。通常は`string`または[`VFile`][vfile]。`new VFile(x)`の`x`として受け入れられる任意の値
* `done` ([`ProcessCallback`][api-process-callback]、オプション) — コールバック

###### 戻り値

`done`が与えられた場合は何も返しません（`undefined`）。
それ以外の場合は、致命的なエラーで拒否されるか、処理されたファイルで解決されるプロミス（[`Promise<VFile>`][vfile]）。

解析され、変換され、コンパイルされた値は`file.value`で利用可能です（注意を参照）。

> 👉 **注**: unifiedは通常、シリアル化によってコンパイルします：ほとんどのコンパイラは`string`（または`Uint8Array`）を返します。
> [`rehype-react`][rehype-react]で構成されたものなど、一部のコンパイラは他の値（この場合はReactツリー）を返します。
> シリアル化しないコンパイラを使用している場合は、異なる結果値を予期してください。
>
> TypeScriptでカスタム結果を登録するには、[`CompileResultMap`][api-compile-result-map]に追加してください。

###### 例

この例は、`process`を使用してファイルを処理する方法を示しています：

```js
import rehypeDocument from 'rehype-document'
import rehypeFormat from 'rehype-format'
import rehypeStringify from 'rehype-stringify'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import {unified} from 'unified'

const file = await unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(rehypeDocument, {title: '👋🌍'})
  .use(rehypeFormat)
  .use(rehypeStringify)
  .process('# Hello world!')

console.log(String(file))
```

結果：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>👋🌍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
    <h1>Hello world!</h1>
  </body>
</html>
```

### `processor.processSync(file)`

プロセッサで構成されたとおりに指定されたファイルを処理します。

非同期変換が構成されている場合、エラーが発生します。

> 👉 **注**: `processSync`はプロセッサがまだ*[凍結][api-freeze]*されていない場合、プロセッサを凍結します。

> 👉 **注**: `processSync`は[解析、実行、および文字列化フェーズ][overview]を実行します。

###### パラメータ

* `file` ([`Compatible`][vfile-compatible]、オプション) — ファイル。通常は`string`または[`VFile`][vfile]。`new VFile(x)`の`x`として受け入れられる任意の値

###### 戻り値

処理されたファイル（[`VFile`][vfile]）。

解析され、変換され、コンパイルされた値は`file.value`で利用可能です（注意を参照）。

> 👉 **注**: unifiedは通常、シリアル化によってコンパイルします：ほとんどのコンパイラは`string`（または`Uint8Array`）を返します。
> [`rehype-react`][rehype-react]で構成されたものなど、一部のコンパイラは他の値（この場合はReactツリー）を返します。
> シリアル化しないコンパイラを使用している場合は、異なる結果値を予期してください。
>
> TypeScriptでカスタム結果を登録するには、[`CompileResultMap`][api-compile-result-map]に追加してください。

###### 例

この例は、すべての変換が同期的な場合に`processSync`を使用してファイルを処理する方法を示しています。

```js
import rehypeDocument from 'rehype-document'
import rehypeFormat from 'rehype-format'
import rehypeStringify from 'rehype-stringify'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import {unified} from 'unified'

const processor = unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(rehypeDocument, {title: '👋🌍'})
  .use(rehypeFormat)
  .use(rehypeStringify)

console.log(String(processor.processSync('# Hello world!')))
```

結果：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>👋🌍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
    <h1>Hello world!</h1>
  </body>
</html>
```

### `processor.run(tree[, file][, done])`

構文ツリーに対して*[トランスフォーマー][api-transformer]*を実行します。

> 👉 **注**: `run`はプロセッサがまだ*[凍結][api-freeze]*されていない場合、プロセッサを凍結します。

> 👉 **注**: `run`は[実行フェーズ][overview]を実行し、他のフェーズは実行しません。

###### 署名

* `processor.run(tree, done)`
* `processor.run(tree, file, done)`
* `Promise<Node> = processor.run(tree, file?)`

###### パラメータ

* `tree` ([`Node`][node]) — 変換および検査するツリー
* `file` ([`Compatible`][vfile-compatible]、オプション) — `node`に関連付けられたファイル。`new VFile(x)`の`x`として受け入れられる任意の値
* `done` ([`RunCallback`][api-run-callback]、オプション) — コールバック

###### 戻り値

`done`が与えられた場合は何も返しません（`undefined`）。
それ以外の場合、致命的なエラーで拒否されるか、変換されたツリーで解決されるプロミス（[`Promise<Node>`][node]）。

###### 例

この例は、`run`を使用してツリーを変換する方法を示しています：

```js
import remarkReferenceLinks from 'remark-reference-links'
import {unified} from 'unified'
import {u} from 'unist-builder'

const tree = u('root', [
  u('paragraph', [
    u('link', {href: 'https://example.com'}, [u('text', 'Example Domain')])
  ])
])

const changedTree = await unified().use(remarkReferenceLinks).run(tree)

console.log(changedTree)
```

結果：

```js
{
  type: 'root',
  children: [
    {type: 'paragraph', children: [Array]},
    {type: 'definition', identifier: '1', title: '', url: undefined}
  ]
}
```

### `processor.runSync(tree[, file])`

構文ツリーに対して*[トランスフォーマー][api-transformer]*を実行します。

非同期変換が構成されている場合、エラーが発生します。

> 👉 **注**: `runSync`はプロセッサがまだ*[凍結][api-freeze]*されていない場合、プロセッサを凍結します。

> 👉 **注**: `runSync`は[実行フェーズ][overview]を実行し、他のフェーズは実行しません。

###### パラメータ

* `tree` ([`Node`][node]) — 変換および検査するツリー
* `file` ([`Compatible`][vfile-compatible]、オプション) — `node`に関連付けられたファイル。`new VFile(x)`の`x`として受け入れられる任意の値

###### 戻り値

変換されたツリー（[`Node`][node]）。

### `processor.stringify(tree[, file])`

構文ツリーをコンパイルします。

> 👉 **注**: `stringify`はプロセッサがまだ*[凍結][api-freeze]*されていない場合、プロセッサを凍結します。

> 👉 **注**: `stringify`は[文字列化フェーズ][overview]を実行し、実行フェーズや他のフェーズは実行しません。

###### パラメータ

* `tree` ([`Node`][node]) — コンパイルするツリー
* `file` ([`Compatible`][vfile-compatible]、オプション) — `node`に関連付けられたファイル。`new VFile(x)`の`x`として受け入れられる任意の値

###### 戻り値

ツリーのテキスト表現（`Uint8Array`または`string`、注意を参照）。

> 👉 **注**: unifiedは通常、シリアル化によってコンパイルします：ほとんどのコンパイラは`string`（または`Uint8Array`）を返します。
> [`rehype-react`][rehype-react]で構成されたものなど、一部のコンパイラは他の値（この場合はReactツリー）を返します。
> シリアル化しないコンパイラを使用している場合は、異なる結果値を予期してください。
>
> TypeScriptでカスタム結果を登録するには、[`CompileResultMap`][api-compile-result-map]に追加してください。

###### 例

この例は、`stringify`を使用して構文ツリーをシリアル化する方法を示しています：

```js
import {h} from 'hastscript'
import rehypeStringify from 'rehype-stringify'
import {unified} from 'unified'

const tree = h('h1', 'Hello world!')

const document = unified().use(rehypeStringify).stringify(tree)

console.log(document)
```

結果：

```html
<h1>Hello world!</h1>
```

### `processor.use(plugin[, options])`

プラグイン、使用可能な値のリスト、またはプリセットを使用するようにプロセッサを構成します。

プロセッサがすでにプラグインを使用している場合、渡されたオプションに基づいて以前のプラグイン構成が変更されます。
言い換えれば、プラグインは2回目に追加されません。

> 👉 **注**: `use`は[*凍結された*][api-freeze]プロセッサで呼び出すことはできません。
> まずプロセッサを呼び出して、新しい凍結されていないプロセッサを作成してください。

###### 署名

* `processor.use(preset?)`
* `processor.use(list)`
* `processor.use(plugin[, ...parameters])`

###### パラメータ

* `preset` ([`Preset`][api-preset]) — プラグインと設定
* `list` ([`PluggableList`][api-pluggable-list]) — 使用可能なもののリスト
* `plugin` ([`Plugin`][api-plugin]) — プラグイン
* `parameters` (`Array<unknown>`) — `plugin`の構成、通常は単一のオプションオブジェクト

###### 戻り値

現在のプロセッサ（[`processor`][api-processor]）。

###### 例

`.use()`にプラグインを渡す方法は多数あります。
この例は概要を示しています：

```js
import {unified} from 'unified'

unified()
  // オプション付きのプラグイン：
  .use(pluginA, {x: true, y: true})
  // 同じプラグインを再度渡すと構成がマージされます（`{x: true, y: false, z: true}`に）：
  .use(pluginA, {y: false, z: true})
  // プラグイン：
  .use([pluginB, pluginC])
  // 2つのプラグイン、2つ目にはオプション付き：
  .use([pluginD, [pluginE, {}]])
  // プラグインと設定付きのプリセット：
  .use({plugins: [pluginF, [pluginG, {}]], settings: {position: false}})
  // 設定のみ：
  .use({settings: {position: false}})
```

### `CompileResultMap`

コンパイラからの既知の結果のインターフェース（TypeScriptタイプ）。

通常、コンパイラは`vfile`の[`Value`][vfile-value]としてテキストを結果として返します。
Reactノードのような他のものにコンパイルする場合（例：`rehype-react`）、このインターフェースを拡張してそのタイプを含めることができます。

```ts
import type {ReactNode} from 'somewhere'

declare module 'unified' {
  interface CompileResultMap {
    // 新しい結果を登録します（値が使用され、キーはそれに一致する必要があります）。
    ReactNode: ReactNode
  }
}

export {} // これは必要ない場合がありますが、ファイルがモジュールであることを確認します。
```

値にアクセスするには[`CompileResults`][api-compile-results]を使用してください。

###### タイプ

```ts
interface CompileResultMap {
  // 注意：`VFile`の`Value`が変更された場合、これも変更する必要があります。
  Uint8Array: Uint8Array
  string: string
}
```

### `CompileResults`

コンパイラからの許容可能な結果（TypeScriptタイプ）。

カスタム結果を登録するには、[`CompileResultMap`][api-compile-result-map]に追加してください。

###### タイプ

```ts
type CompileResults = CompileResultMap[keyof CompileResultMap]
```

### `Compiler`

**コンパイラ**は構文ツリーを他のもの（ほとんどの場合はテキスト）にコンパイルする処理を行います（TypeScriptタイプ）。

文字列化フェーズで使用され、コンパイルする文書の[`Node`][node]と[`VFile`][vfile]表現と共に呼び出されます。
与えられたツリーのテキスト表現（通常は`string`）を返す必要があります。

> 👉 **注意**: unifiedは通常、シリアライズによってコンパイルします：ほとんどのコンパイラは`string`（または`Uint8Array`）を返します。
> [`rehype-react`][rehype-react]で構成されているものなど、一部のコンパイラは他の値（この場合はReactツリー）を返します。
> シリアライズしないコンパイラを使用している場合は、異なる結果値を予期してください。
>
> TypeScriptでカスタム結果を登録するには、[`CompileResultMap`][api-compile-result-map]に追加してください。

###### タイプ

```ts
type Compiler
  Tree extends Node = Node,
  Result extends CompileResults = CompileResults
> = (tree: Tree, file: VFile) => Result
```

### `Data`

すべてのプラグインでサポートできる既知のデータのインターフェース（TypeScriptタイプ）。

通常、オプションは特定のプラグインに与えることができますが、複数のプラグインで情報を共有することが意味を持つ場合もあります。
例えば、すべてのフェーズで必要な自己閉じHTML要素のリストなどです。

これをタイプ化するには、次のようにします：

```ts
declare module 'unified' {
  interface Data {
    htmlVoidElements?: Array<string> | undefined
  }
}

export {} // これは必要ない場合がありますが、ファイルがモジュールであることを確認します。
```

###### タイプ

```ts
interface Data {
  settings?: Settings | undefined
}
```

詳細については[`Settings`][api-settings]を参照してください。

### `Parser`

**パーサー**はテキストを構文ツリーに解析する処理を行います（TypeScriptタイプ）。

解析フェーズで使用され、解析する文書の`string`と[`VFile`][vfile]と共に呼び出されます。
与えられたファイルの構文ツリー表現（[`Node`][node]）を返す必要があります。

###### タイプ

```ts
type Parser<Tree extends Node = Node> = (document: string, file: VFile) => Tree
```

### `Pluggable`

プラグインと設定を追加するさまざまな方法の結合（TypeScriptタイプ）。

###### タイプ

```ts
type Pluggable =
  | Plugin<Array<any>, any, any>
  | PluginTuple<Array<any>, any, any>
  | Preset
```

詳細については[`Plugin`][api-plugin]、[`PluginTuple`][api-plugin-tuple]、
および[`Preset`][api-preset]を参照してください。

### `PluggableList`

プラグインとプリセットのリスト（TypeScriptタイプ）。

###### タイプ

```ts
type PluggableList = Array<Pluggable>
```

詳細については[`Pluggable`][api-pluggable]を参照してください。

### `Plugin`

単一のプラグイン（TypeScriptタイプ）。

プラグインは、適用されるプロセッサを以下の方法で構成します：

* パーサー、コンパイラ、またはデータの構成などによってプロセッサを変更します
* ツリーとファイルの処理方法を指定します

実際には、オプションを受け取り、プロセッサ（`this`）を構成できる関数です。

> 👉 **注意**: プラグインは、適用されるときではなく、プロセッサが*凍結*されたときに呼び出されます。

###### タイプ

```ts
type Plugin
  PluginParameters extends unknown[] = [],
  Input extends Node | string | undefined = Node,
  Output = Input
> = (
  this: Processor,
  ...parameters: PluginParameters
) => Input extends string // Parser.
  ? Output extends Node | undefined
    ? undefined | void
    : never
  : Output extends CompileResults // Compiler.
  ? Input extends Node | undefined
    ? undefined | void
    : never
  : // Inspect/transform.
      | Transformer
          Input extends Node ? Input : Node,
          Output extends Node ? Output : Node
        >
      | undefined
      | void
```

詳細については[`Transformer`][api-transformer]を参照してください。

###### 例

`move.js`:

```js
/**
 * @import {Plugin} from 'unified'
 */

/**
 * @typedef Options
 *   構成（必須）。
 * @property {string} extname
 *   使用するファイル拡張子（`.`で始まる必要があります）。
 */

/** @type {Plugin<[Options]>} */
export function move(options) {
  if (!options || !options.extname) {
    throw new Error('Missing `options.extname`')
  }

  return function (_, file) {
    if (file.extname && file.extname !== options.extname) {
      file.extname = options.extname
    }
  }
}
```

`example.md`:

```markdown
# Hello, world!
```

`example.js`:

```js
import rehypeStringify from 'rehype-stringify'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import {read, write} from 'to-vfile'
import {unified} from 'unified'
import {reporter} from 'vfile-reporter'
import {move} from './move.js'

const file = await unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(move, {extname: '.html'})
  .use(rehypeStringify)
  .process(await read('example.md'))

console.error(reporter(file))
await write(file) // `example.html`に書き込まれます。
```

結果：

```txt
example.md: 問題は見つかりませんでした
```

...そして `example.html` には：

```html
<h1>Hello, world!</h1>
```

### `PluginTuple`

プラグインとその構成のタプル（TypeScriptタイプ）。

最初の項目はプラグインで、残りはそのパラメータです。

###### タイプ

```ts
type PluginTuple
  TupleParameters extends unknown[] = [],
  Input extends Node | string | undefined = undefined,
  Output = undefined
> = [
  plugin: Plugin<TupleParameters, Input, Output>,
  ...parameters: TupleParameters
]
```

詳細については[`Plugin`][api-plugin]を参照してください。

### `Preset`

共有可能な構成（TypeScriptタイプ）。

プラグインと設定を含むことができます。

###### フィールド

* `plugins` ([`PluggableList`][api-pluggable-list], オプション)
  — プラグインとプリセットのリスト
* `settings` ([`Data`][api-data], オプション)
  — パーサーとコンパイラの共有設定

###### 例

`preset.js`:

```js
/**
 * @import {Preset} from 'unified'
 */

import remarkCommentConfig from 'remark-comment-config'
import remarkLicense from 'remark-license'
import remarkPresetLintConsistent from 'remark-preset-lint-consistent'
import remarkPresetLintRecommended from 'remark-preset-lint-recommended'
import remarkToc from 'remark-toc'

/** @type {Preset} */
const preset = {
  plugins: [
    remarkPresetLintRecommended,
    remarkPresetLintConsistent,
    remarkCommentConfig,
    [remarkToc, {maxDepth: 3, tight: true}],
    remarkLicense
  ],
  settings: {bullet: '*', emphasis: '*', fences: true},
}

export default preset
```

`example.md`:

```markdown
# Hello, world!

_Emphasis_ and **importance**.

## Table of contents

## API

## License
```

`example.js`:

```js
import {remark} from 'remark'
import {read, write} from 'to-vfile'
import {reporter} from 'vfile-reporter'
import preset from './preset.js'

const file = await remark()
  .use(preset)
  .process(await read('example.md'))

console.error(reporter(file))
await write(file)
```

結果：

```txt
example.md: 問題は見つかりませんでした
```

`example.md`は次のような内容になります：

```markdown
# Hello, world!

*Emphasis* and **importance**.

## Table of contents

*   [API](#api)
*   [License](#license)

## API

## License

[MIT](license) © [Titus Wormer](https://wooorm.com)
```

### `ProcessCallback`

プロセスが完了したときに呼び出されるコールバック（TypeScriptタイプ）。

エラーまたは結果のいずれかで呼び出されます。

###### パラメータ

* `error` (`Error`, オプション)
  — 致命的なエラー
* `file` ([`VFile`][vfile], オプション)
  — 処理されたファイル

###### 戻り値

なし（`undefined`）。

###### 例

この例は、`process`を使用してコールバックでファイルを処理する方法を示しています。

```js
import remarkGithub from 'remark-github'
import remarkParse from 'remark-parse'
import remarkStringify from 'remark-stringify'
import {unified} from 'unified'
import {reporter} from 'vfile-reporter'

unified()
  .use(remarkParse)
  .use(remarkGithub)
  .use(remarkStringify)
  .process('@unifiedjs', function (error, file) {
    if (error) throw error
    if (file) {
      console.error(reporter(file))
      console.log(String(file))
    }
  })
```

結果：

```txt
問題は見つかりませんでした
```

```markdown
[**@unifiedjs**](https://github.com/unifiedjs)
```

### `Processor`

[`processor`][api-processor]のタイプ（TypeScriptタイプ）。

### `RunCallback`

トランスフォーマーが完了したときに呼び出されるコールバック（TypeScriptタイプ）。

エラーまたは結果のいずれかで呼び出されます。

###### パラメータ

* `error` (`Error`, オプション)
  — 致命的なエラー
* `tree` ([`Node`][node], オプション)
  — 変換されたツリー
* `file` ([`VFile`][vfile], オプション)
  — ファイル

###### 戻り値

なし（`undefined`）。

### `Settings`

パーサーとコンパイラでサポートできる既知の追加オプションのインターフェース。

これは、`remark`のようなパッケージを使用できるようにするために存在します。これらのパッケージは、パーサーとコンパイラの両方を構成し（この場合は`remark-parse`と`remark-stringify`）、それらのオプションを提供できるようにします。

パーサーやコンパイラを作成する際、それらが一緒にパッケージ化される可能性がある場合は、`this.data('settings')`を入力としてサポートし、明示的に渡された`options`とマージする必要があります。
そして、タイプ化するには、`remark-stringify`を例にして次のようにします：

```ts
declare module 'unified' {
  interface Settings {
    bullet: '*' | '+' | '-'
    // …
  }
}

export {} // これは必要ない場合がありますが、ファイルがモジュールであることを確認します。
```

###### タイプ

```ts
interface Settings {}
```

### `TransformCallback`

トランスフォームに渡されるコールバック（TypeScriptタイプ）。

`transformer`の署名が3番目の引数を受け入れる場合、トランスフォーマーは非同期操作を実行でき、それを呼び出す必要があります。

###### パラメータ

* `error` (`Error`, オプション)
  — プロセスを停止する致命的なエラー
* `tree` ([`Node`][node], オプション)
  — 新しい、変更されたツリー
* `file` ([`VFile`][vfile], オプション)
  — 新しい、変更されたファイル

###### 戻り値

なし（`undefined`）。

### `Transformer`

トランスフォーマーは構文ツリーとファイルを処理します（TypeScriptタイプ）。

これらは、実行フェーズを通過するたびに構文ツリーとファイルで呼び出される関数です。
エラーが発生すると（投げられたか、返されたか、拒否されたか、`next`に渡されたかにかかわらず）、プロセスは停止します。

実行フェーズは[`trough`][trough]によって処理されます。これらの関数の正確な意味については、そのドキュメントを参照してください。

> 👉 **注意**: `next`は無視する可能性が高いです：受け入れないでください。
> これはコールバックスタイルの非同期作業をサポートします。
> しかし、プロミスの方が理解しやすい可能性があります。

###### タイプ

```ts
type Transformer
  Input extends Node = Node,
  Output extends Node = Input
> = (
  tree: Input,
  file: VFile,
  next: TransformCallback<Output>
) =>
  | Promise<Output | undefined>
  | Output
  | Error
  | undefined
```

## タイプ

このパッケージは[TypeScript][]で完全に型付けされています。
追加のタイプとして以下をエクスポートします：
[`CompileResultMap`][api-compile-result-map]、
[`CompileResults`][api-compile-results]、
[`Compiler`][api-compiler]、
[`Data`][api-data]、
[`Parser`][api-parser]、
[`Pluggable`][api-pluggable]、
[`PluggableList`][api-pluggable-list]、
[`Plugin`][api-plugin]、
[`PluginTuple`][api-plugin-tuple]、
[`Preset`][api-preset]、
[`ProcessCallback`][api-process-callback]、
[`Processor`][api-processor]、
[`RunCallback`][api-run-callback]、
[`Settings`][api-settings]、
[`TransformCallback`][api-transform-callback]、
および[`Transformer`][api-transformer]

TypeScriptが機能するためには、特にプラグインを正しく型付けすることが重要です。
`Plugin`型をジェネリックと共に使用し、私たちのパッケージが提供する構文ツリーのノードタイプを使用することを強くお勧めします（例：
[`@types/hast`][types-hast]、[`@types/mdast`][types-mdast]、
[`@types/nlcst`][types-nlcst]）。

```js
/**
 * @import {Root as HastRoot} from 'hast'
 * @import {Root as MdastRoot} from 'mdast'
 * @import {Plugin} from 'unified'
 */

/**
 * @typedef Options
 *   設定（オプション）。
 * @property {boolean | null | undefined} [someField]
 *   いくつかのオプション（オプション）。
 */

// オプションを型付けするには：
/** @type {Plugin<[(Options | null | undefined)?]>} */
export function myPluginAcceptingOptions(options) {
  const settings = options || {}
  // `settings`は現在`Options`です。
}

// 特定のツリーで動作するプラグインを型付けするには（オプションなし）：
/** @type {Plugin<[], MdastRoot>} */
export function myRemarkPlugin() {
  return function (tree, file) {
    // `tree`は`MdastRoot`です。
  }
}

// あるツリーを別のツリーに変換するプラグインを型付けするには：
/** @type {Plugin<[], MdastRoot, HastRoot>} */
export function remarkRehype() {
  return function (tree) {
    // `tree`は`MdastRoot`です。
    // 結果は`HastRoot`でなければなりません。
  }
}

// パーサーを定義するプラグインを型付けするには：
/** @type {Plugin<[], string, MdastRoot>} */
export function remarkParse(options) {}

// コンパイラを定義するプラグインを型付けするには：
/** @type {Plugin<[], HastRoot, string>} */
export function rehypeStringify(options) {}
```

## 互換性

unified collectiveによって維持されるプロジェクトは、維持されているバージョンのNode.jsと互換性があります。

新しいメジャーリリースを行う際、サポートされていないバージョンのNodeのサポートを終了します。
これは、現在のリリースライン `unified@^11` をNode.js 16と互換性を保つよう努めていることを意味します。

## 貢献

始めるための方法については、[`unifiedjs/.github`][health]の[`contributing.md`][contributing]を参照してください。
支援を受ける方法については[`support.md`][support]を参照してください。

このプロジェクトには[行動規範][coc]があります。
このリポジトリ、組織、またはコミュニティと対話することで、あなたはその条件に従うことに同意したものとみなされます。

セキュリティレポートを提出する方法については、[セキュリティポリシー][security]を参照してください。

## スポンサー

この取り組みを支援し、[OpenCollective][collective]でスポンサーになることで還元しましょう！

<table>
<tr valign="middle">
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://vercel.com">Vercel</a><br><br>
  <a href="https://vercel.com"><img src="https://avatars1.githubusercontent.com/u/14985020?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://motif.land">Motif</a><br><br>
  <a href="https://motif.land"><img src="https://avatars1.githubusercontent.com/u/74457950?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.hashicorp.com">HashiCorp</a><br><br>
  <a href="https://www.hashicorp.com"><img src="https://avatars1.githubusercontent.com/u/761456?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://americanexpress.io">American Express</a><br><br>
  <a href="https://americanexpress.io"><img src="https://avatars1.githubusercontent.com/u/3853301?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.gitbook.com">GitBook</a><br><br>
  <a href="https://www.gitbook.com"><img src="https://avatars1.githubusercontent.com/u/7111340?s=256&v=4" width="128"></a>
</td>
</tr>
<tr valign="middle">
</tr>
<tr valign="middle">
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.gatsbyjs.org">Gatsby</a><br><br>
  <a href="https://www.gatsbyjs.org"><img src="https://avatars1.githubusercontent.com/u/12551863?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.netlify.com">Netlify</a><br><br>
  <!--OC has a sharper image-->
  <a href="https://www.netlify.com"><img src="https://images.opencollective.com/netlify/4087de2/logo/256.png" width="128"></a>
</td>
<td width="10%" align="center">
  <a href="https://www.coinbase.com">Coinbase</a><br><br>
  <a href="https://www.coinbase.com"><img src="https://avatars1.githubusercontent.com/u/1885080?s=256&v=4" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://themeisle.com">ThemeIsle</a><br><br>
  <a href="https://themeisle.com"><img src="https://avatars1.githubusercontent.com/u/58979018?s=128&v=4" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://expo.io">Expo</a><br><br>
  <a href="https://expo.io"><img src="https://avatars1.githubusercontent.com/u/12504344?s=128&v=4" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://boostnote.io">Boost Note</a><br><br>
  <a href="https://boostnote.io"><img src="https://images.opencollective.com/boosthub/6318083/logo/128.png" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://markdown.space">Markdown Space</a><br><br>
  <a href="https://markdown.space"><img src="https://images.opencollective.com/markdown-space/e1038ed/logo/128.png" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://www.holloway.com">Holloway</a><br><br>
  <a href="https://www.holloway.com"><img src="https://avatars1.githubusercontent.com/u/35904294?s=128&v=4" width="64"></a>
</td>
</tr>
<tr valign="middle">
<td width="100%" align="center" colspan="6">
  <br>
  <a href="https://opencollective.com/unified"><strong>あなたも？</strong></a>
  <br><br>
</td>
</tr>
</table>

## 謝辞

unifiedの予備的な作業は**[retext][]**のために[2014年に行われ][preliminary]、[`ware`][ware]にインスパイアされました。
さらなる成熟は**[remark][]**で行われました。
プロジェクトは最終的に2015年に[外部化][externalised]され、`unified`として[公開][published]されました。
プロジェクトは**[@wooorm](https://github.com/wooorm)**によって作成されました。

`unified`はその後プラグインアーキテクチャを[`trough`][trough]に移行しましたが、
**[@calvinfo](https://github.com/calvinfo)**、
**[@ianstormtaylor](https://github.com/ianstormtaylor)**、そして他の方々の[`ware`][ware]に対する作業に感謝します。それは初期の大きなインスピレーションとなりました。

## ライセンス

[MIT][license] © [Titus Wormer][author]

<!-- 定義 -->

[logo]: https://raw.githubusercontent.com/unifiedjs/unified/93862e5/logo.svg?sanitize=true

[build-badge]: https://github.com/unifiedjs/unified/workflows/main/badge.svg

[build]: https://github.com/unifiedjs/unified/actions

[coverage-badge]: https://img.shields.io/codecov/c/github/unifiedjs/unified.svg

[coverage]: https://codecov.io/github/unifiedjs/unified

[downloads-badge]: https://img.shields.io/npm/dm/unified.svg

[downloads]: https://www.npmjs.com/package/unified

[size-badge]: https://img.shields.io/bundlejs/size/unified

[size]: https://bundlejs.com/?q=unified

[sponsors-badge]: https://opencollective.com/unified/sponsors/badge.svg

[backers-badge]: https://opencollective.com/unified/backers/badge.svg

[collective]: https://opencollective.com/unified

[chat-badge]: https://img.shields.io/badge/chat-discussions-success.svg

[chat]: https://github.com/unifiedjs/unified/discussions

[esm]: https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c

[esmsh]: https://esm.sh

[typescript]: https://www.typescriptlang.org

[health]: https://github.com/unifiedjs/.github

[contributing]: https://github.com/unifiedjs/.github/blob/main/contributing.md

[support]: https://github.com/unifiedjs/.github/blob/main/support.md

[coc]: https://github.com/unifiedjs/.github/blob/main/code-of-conduct.md

[security]: https://github.com/unifiedjs/.github/blob/main/security.md

[license]: license

[author]: https://wooorm.com

[npm]: https://docs.npmjs.com/cli/install

[site]: https://unifiedjs.com

[twitter]: https://twitter.com/unifiedjs

[rehype]: https://github.com/rehypejs/rehype

[remark]: https://github.com/remarkjs/remark

[retext]: https://github.com/retextjs/retext

[syntax-tree]: https://github.com/syntax-tree

[esast]: https://github.com/syntax-tree/esast

[hast]: https://github.com/syntax-tree/hast

[mdast]: https://github.com/syntax-tree/mdast

[nlcst]: https://github.com/syntax-tree/nlcst

[unist]: https://github.com/syntax-tree/unist

[xast]: https://github.com/syntax-tree/xast

[unified-engine]: https://github.com/unifiedjs/unified-engine

[unified-args]: https://github.com/unifiedjs/unified-args

[unified-engine-gulp]: https://github.com/unifiedjs/unified-engine-gulp

[unified-language-server]: https://github.com/unifiedjs/unified-language-server

[unified-stream]: https://github.com/unifiedjs/unified-stream

[rehype-remark]: https://github.com/rehypejs/rehype-remark

[rehype-retext]: https://github.com/rehypejs/rehype-retext

[remark-rehype]: https://github.com/remarkjs/remark-rehype

[remark-retext]: https://github.com/remarkjs/remark-retext

[node]: https://github.com/syntax-tree/unist#node

[vfile]: https://github.com/vfile/vfile

[vfile-compatible]: https://github.com/vfile/vfile#compatible

[vfile-value]: https://github.com/vfile/vfile#value

[vfile-utilities]: https://github.com/vfile/vfile#list-of-utilities

[rehype-react]: https://github.com/rehypejs/rehype-react

[trough]: https://github.com/wooorm/trough#function-fninput-next

[rehype-plugins]: https://github.com/rehypejs/rehype/blob/main/doc/plugins.md#list-of-plugins

[remark-plugins]: https://github.com/remarkjs/remark/blob/main/doc/plugins.md#list-of-plugins

[retext-plugins]: https://github.com/retextjs/retext/blob/main/doc/plugins.md#list-of-plugins

[awesome-rehype]: https://github.com/rehypejs/awesome-rehype

[awesome-remark]: https://github.com/remarkjs/awesome-remark

[awesome-retext]: https://github.com/retextjs/awesome-retext

[topic-rehype-plugin]: https://github.com/topics/rehype-plugin

[topic-remark-plugin]: https://github.com/topics/remark-plugin

[topic-retext-plugin]: https://github.com/topics/retext-plugin

[types-hast]: https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/hast

[types-mdast]: https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/mdast

[types-nlcst]: https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/nlcst

[preliminary]: https://github.com/retextjs/retext/commit/8fcb1f

[externalised]: https://github.com/remarkjs/remark/commit/9892ec

[published]: https://github.com/unifiedjs/unified/commit/2ba1cf

[ware]: https://github.com/segmentio/ware

[api]: #api

[contribute]: #contribute

[overview]: #overview

[sponsor]: #sponsor

[api-compile-result-map]: #compileresultmap

[api-compile-results]: #compileresults

[api-compiler]: #compiler

[api-data]: #data

[api-freeze]: #processorfreeze

[api-parser]: #parser

[api-pluggable]: #pluggable

[api-pluggable-list]: #pluggablelist

[api-plugin]: #plugin

[api-plugin-tuple]: #plugintuple

[api-preset]: #preset

[api-process]: #processorprocessfile-done

[api-process-callback]: #processcallback

[api-processor]: #processor

[api-run-callback]: #runcallback

[api-settings]: #settings

[api-transform-callback]: #transformcallback

[api-transformer]: #transformer