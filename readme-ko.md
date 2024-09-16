# [![unified][logo]][site]

[![Build][build-badge]][build]
[![Coverage][coverage-badge]][coverage]
[![Downloads][downloads-badge]][downloads]
[![Size][size-badge]][size]
[![Sponsors][sponsors-badge]][collective]
[![Backers][backers-badge]][collective]
[![Chat][chat-badge]][chat]

**unified**는 플러그인을 사용하여 콘텐츠를 검사하고 변환할 수 있게 해줍니다.

## 목차

* [이것은 무엇인가요?](#what-is-this)
* [언제 이것을 사용해야 하나요?](#when-should-i-use-this)
* [설치](#install)
* [사용](#use)
* [개요](#overview)
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
* [타입](#types)
* [호환성](#호환성)
* [기여하기](#기여하기)
* [후원](#후원)
* [감사의 말](#감사의-말)
* [라이선스](#라이선스)

## 이것은 무엇인가요?

unified는 두 가지입니다:

* **unified**는 구조화된 데이터(AST)로 콘텐츠를 다루는 500개 이상의 무료 오픈 소스 패키지의 집합체입니다
* `unified` (이 프로젝트)는 GitHub에서 130만 개 이상의 프로젝트에서 사용되는 핵심 패키지로, 플러그인을 통해 콘텐츠를 처리합니다

다양한 종류의 콘텐츠를 다루는 여러 생태계가 unified를 기반으로 구축되어 있습니다.
특히, [remark][] (마크다운), [rehype][] (HTML), [retext][] (자연어)가 있습니다.
이러한 생태계들은 서로 연결될 수 있습니다.

* 우리에 대해 더 알아보려면 [`unifiedjs.com`][site]을 참조하세요
* 업데이트를 확인하려면 Twitter에서 [@unifiedjs][twitter]를 팔로우하세요
* 질문이 있다면 [support][]를 참조하세요
* 도움을 주고 싶다면 아래의 [contribute][]와 [sponsor][]를 참조하세요

## 언제 이것을 사용해야 하나요?

일부 경우에는 이미 unified를 사용하고 있을 수 있습니다.
예를 들어, MDX, Gatsby, Docusaurus 등에서 사용됩니다.
이런 경우에는 `unified`를 직접 추가할 필요는 없지만 해당 프로젝트에 플러그인을 포함시킬 수 있습니다.

하지만 실제로 재미있는 부분(일부 사람들에게는)은 직접 손을 더럽히고 구문 트리를 다루며 직접 구축하는 것입니다.
이를 통해 그런 프로젝트들을 만들거나, Prettier와 같은 것들, 또는 자신만의 사이트 생성기를 만들 수 있습니다.
유틸리티들을 연결하고 문제를 확인하거나 한 형식에서 다른 형식으로 변환하는 자신만의 플러그인을 만들 수 있습니다.

한 가지 유형의 콘텐츠(예: 마크다운)만 다루는 경우, 해당 생태계의 메인 패키지(즉, `remark`)를 대신 사용할 수 있습니다.
여러 종류의 콘텐츠(예: 마크다운과 HTML)를 다루는 경우, `unified` 자체를 사용하고 필요한 플러그인을 선택하여 사용하는 것이 좋습니다.

## 설치

이 패키지는 [ESM 전용][esm]입니다.
Node.js(버전 16+)에서는 [npm][]을 통해 설치하세요:

```sh
npm install unified
```

Deno에서는 [`esm.sh`][esmsh]를 사용하세요:

```js
import {unified} from 'https://esm.sh/unified@11'
```

브라우저에서는 [`esm.sh`][esmsh]를 사용하세요:

```html
<script type="module">
  import {unified} from 'https://esm.sh/unified@11?bundle'
</script>
```

## 사용

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

결과:

```txt
no issues found
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

<!-- 이전 이름: -->

<a name="description"></a>

## 개요

`unified`는 구문 트리를 사용하여 콘텐츠를 처리하는 인터페이스입니다.
구문 트리는 프로그램이 이해할 수 있는 콘텐츠의 표현입니다.
이러한 프로그램을 *[플러그인][api-plugin]* 이라고 하며, 이들은 트리를 가져와 검사하고 수정합니다.
텍스트에서 구문 트리를 얻기 위해 *[파서][api-parser]* 가 있습니다.
다시 텍스트로 돌아가기 위해 *[컴파일러][api-compiler]* 가 있습니다.
이것이 프로세서의 *[프로세스][api-process]* 입니다.

```ascii
| ........................ 프로세스 ..........................|
| .......... 파싱 ... | ... 실행 ... | ... 문자열화 ...........|

         +--------+                     +----------+
입력 ->- |  파서   | ->-  구문 트리  ->- |  컴파일러 | ->- 출력
         +--------+          |          +----------+
                             X
                             |
                       +------------+
                       | 트랜스포머  |
                       +------------+
```

###### 프로세서

프로세서는 콘텐츠를 처리합니다.
단독으로는 `unified` (루트 프로세서)가 작동하지 않습니다.
작동하려면 플러그인으로 구성되어야 합니다.
예를 들어:

```js
const processor = unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(rehypeDocument, {title: '👋🌍'})
  .use(rehypeFormat)
  .use(rehypeStringify)
```

이 프로세서는 다양한 작업을 수행할 수 있습니다.
다음과 같은 작업을 할 수 있습니다:

* …마크다운 파싱 (`parse`)
* …파싱된 마크다운을 HTML로 변환하고 HTML 포맷팅 (`run`)
* …HTML 컴파일 (`stringify`)
* …위의 모든 작업 수행 (`process`)

모든 프로세서는 다른 프로세서를 구현합니다.
프로세서를 생성하려면 다른 프로세서를 호출합니다.
새로운 하위 프로세서는 조상 프로세서와 동일하게 작동하도록 구성됩니다.
그러나 하위 프로세서가 나중에 구성될 때 조상 프로세서에 영향을 미치지 않습니다.

프로세서가 모듈에서 노출될 때(예: `unified` 자체) 직접 구성해서는 안 됩니다. 그렇게 하면 모든 모듈 사용자에 대해 동작이 변경될 수 있기 때문입니다.
이러한 프로세서는 *[동결][api-freeze]* 되어 있으며 사용하기 전에 호출하여 새 프로세서를 생성해야 합니다.

###### 파일

문서를 처리할 때 해당 문서에 대한 메타데이터가 수집됩니다.
[`vfile`][vfile]은 unified와 플러그인을 위해 파일에 대한 데이터, 메타데이터 및 메시지를 저장하는 파일 형식입니다.

이러한 파일 작업을 위한 여러 [유틸리티][vfile-utilities]가 있습니다.

###### 구문 트리

unified에서 사용되는 구문 트리는 [unist][] 노드입니다.
트리는 전체 문서를 나타내며 각 [노드][node]는 `type` 필드가 있는 일반 JavaScript 객체입니다.
노드의 의미와 구문 트리의 형식은 다른 프로젝트에서 정의됩니다:

* [esast][] — JavaScript
* [hast][] — HTML
* [mdast][] — 마크다운
* [nlcst][] — 자연어
* [xast][] — XML

위에서 언급한 각 프로젝트에 나열되어 있고 [`syntax-tree`][syntax-tree] 조직에서 관리되는 트리 작업을 위한 많은 유틸리티가 있습니다.
이러한 유틸리티는 unified 자체보다 낮은 수준이며 플러그인을 만드는 데 사용할 수 있는 빌딩 블록입니다.

<!-- 이전 이름: -->

<a name="list-of-processors"></a>

###### 생태계

각 구문 트리 주변에는 특정 종류의 콘텐츠에 중점을 둔 생태계가 있습니다.
핵심적으로, 이들은 텍스트를 트리로 파싱하고 그 트리를 다시 텍스트로 컴파일합니다.
또한 최종 사용자가 해당 트리에 대한 지식이 없어도 구문 트리로 작업할 수 있는 플러그인을 제공합니다.

* [rehype][] (hast) — HTML
* [remark][] (mdast) — 마크다운
* [retext][] (nlcst) — 자연어

<a name="list-of-plugins"></a>

###### 플러그인

앞서 언급한 각 생태계에는 다양한 작업을 수행할 수 있는 대규모 플러그인 세트가 함께 제공됩니다.

* [remark 플러그인 목록][remark-plugins] ·
  [`remarkjs/awesome-remark`][awesome-remark] ·
  [`remark-plugin` 주제][topic-remark-plugin]
* [rehype 플러그인 목록][rehype-plugins] ·
  [`rehypejs/awesome-rehype`][awesome-rehype] ·
  [`rehype-plugin` 주제][topic-rehype-plugin]
* [retext 플러그인 목록][retext-plugins] ·
  [`retextjs/awesome-retext`][awesome-retext] ·
  [`retext-plugin` 주제][topic-retext-plugin]

또한 모든 생태계에서 작동하는 몇 가지 플러그인도 있습니다:

* [`unified-diff`](https://github.com/unifiedjs/unified-diff)
  — GitHub Actions와 Travis에서 관련 없는 메시지 무시
* [`unified-infer-git-meta`](https://github.com/unifiedjs/unified-infer-git-meta)
  — Git에서 문서의 메타데이터 추론
* [`unified-message-control`](https://github.com/unifiedjs/unified-message-control)
  — 콘텐츠에서 메시지 활성화, 비활성화 및 무시

###### 구성

프로세서는 [플러그인][api-plugin]이나 [`data`][api-data] 메서드를 통해 구성됩니다.
대부분의 플러그인도 옵션을 통해 구성을 받아들입니다.
각 플러그인의 readme를 참조하여 자세한 정보를 확인하세요.

###### 통합

unified는 [`unified-engine`][unified-engine]을 통해 파일 시스템과 통합될 수 있습니다.
CLI 앱은 [`unified-args`][unified-args]로, Gulp 플러그인은 [`unified-engine-gulp`][unified-engine-gulp]로, 언어 서버는 [`unified-language-server`][unified-language-server]로 만들 수 있습니다.
스트리밍 인터페이스는 [`unified-stream`][unified-stream]으로 만들 수 있습니다.

###### 프로그래밍 인터페이스

`unified`가 제공하는 [API][]를 사용하면 여러 파일을 처리하고 메타데이터(예: lint 메시지)에 액세스할 수 있습니다:

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

결과:

```txt
1:16-1:24 warning Emphasis should use `*` as a marker                                 emphasis-marker remark-lint
1:30-1:34 warning `guys` may be insensitive, use `people`, `persons`, `folks` instead gals-man        retext-equality

⚠ 2 warnings

⚠ 2개의 경고
```

```html
<p><em>Emphasis</em> and <em>stress</em>, you guys!</p>
```

<!-- 이전 이름: -->

<a name="processing-between-syntaxes"></a>

###### 생태계 간 변환

생태계는 두 가지 모드로 결합할 수 있습니다.

**브릿지** 모드는 트리를 한 형식(*원본*)에서 다른 형식(*대상*)으로 변환합니다.
다른 프로세서가 대상 트리에서 실행됩니다.
그 후, 원래 프로세서가 원본 트리로 계속 진행됩니다.

**변형** 모드 또한 구문 트리를 한 형식에서 다른 형식으로 변환합니다.
그러나 원래 프로세서가 대상 트리를 계속 변환합니다.

이전 예제("프로그래밍 인터페이스")에서 `remark-retext`는 브릿지 모드로 사용됩니다: retext가 완료된 후 원본 구문 트리가 유지됩니다. 반면 `remark-rehype`는 변형 모드로 사용됩니다: 새로운 구문 트리를 설정하고 원본 트리는 폐기합니다.

다음 플러그인을 사용하여 생태계를 결합할 수 있습니다:

* [`remark-retext`][remark-retext] — 마크다운을 자연어로 변환
* [`remark-rehype`][remark-rehype] — 마크다운을 HTML로 변환
* [`rehype-retext`][rehype-retext] — HTML을 자연어로 변환
* [`rehype-remark`][rehype-remark] — HTML을 마크다운으로 변환

## API

이 패키지는 식별자 `unified`(루트 `processor`)를 내보냅니다.
기본 내보내기는 없습니다.

### `processor()`

새 프로세서를 생성합니다.

###### 반환값

새로운 *[동결되지 않은][api-freeze]* 프로세서 ([`processor`][api-processor]).

이 프로세서는 조상 프로세서와 동일하게 작동하도록 구성됩니다.
하위 프로세서가 나중에 구성될 때 조상 프로세서에 영향을 미치지 않습니다.

###### 예시

이 예제는 새로운 프로세서를 생성하고(`remark`에서) **stdin**(4)과 **stdout**(4)에 연결하는 방법을 보여줍니다.

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

사용할 컴파일러 ([`Compiler`][api-compiler], 선택 사항).

### `processor.data([key[, value]])`

모든 플러그인에서 사용 가능한 정보로 프로세서를 구성합니다.
정보는 객체에 저장됩니다.

일반적으로 옵션은 특정 플러그인에 전달될 수 있지만, 때로는 여러 플러그인과 정보를 공유하는 것이 의미가 있습니다.
예를 들어, 모든 [단계][overview]에서 필요한 self-closing HTML 요소 목록이 있습니다.

> 👉 **참고**: *[동결된][api-freeze]* 프로세서에서는 정보를 설정할 수 없습니다.
> 먼저 프로세서를 호출하여 새로운 동결되지 않은 프로세서를 생성하세요.

> 👉 **참고**: TypeScript에서 사용자 지정 데이터를 등록하려면 [`Data`][api-data] 인터페이스를 확장하세요.

###### 서명

* `processor = processor.data(key, value)`
* `processor = processor.data(dataset)`
* `value = processor.data(key)`
* `dataset = processor.data()`

###### 매개변수

* `key` ([`keyof Data`][api-data], 선택 사항) — 가져올 필드
* `value` ([`Data[key]`][api-data]) — 설정할 값
* `values` ([`Data`][api-data]) — 설정할 값들

###### 반환값

설정 시 현재 프로세서 ([`processor`][api-processor]), 가져올 때 `key`의 값 ([`Data[key]`][api-data]), 또는 키 없이 가져올 때 전체 데이터셋 ([`Data`][api-data]).

###### 예시

이 예제는 정보를 가져오고 설정하는 방법을 보여줍니다:

```js
import {unified} from 'unified'

const processor = unified().data('alpha', 'bravo')

processor.data('alpha') // => 'bravo'

processor.data() // => {alpha: 'bravo'}

processor.data({charlie: 'delta'})

processor.data() // => {charlie: 'delta'}
```

### `processor.freeze()`

프로세서를 동결합니다.

동결된 프로세서는 확장되어야 하며 직접 구성해서는 안 됩니다.

프로세서가 동결되면 해동할 수 없습니다.
프로세서를 호출하여 동일한 방식으로 작동하는 새 프로세서를 생성할 수 있습니다.

`.freeze()`를 호출하여 프로세서를 명시적으로 동결할 수 있습니다.
`.parse()`, `.run()`, `.runSync()`, `.stringify()`, `.process()`, 또는 `.processSync()`가 호출될 때 프로세서는 자동으로 동결됩니다.

###### 반환값

현재 프로세서 ([`processor`][api-processor]).

###### 예시

이 예제 `index.js`는 `rehype`가 자체 확장을 방지하는 방법을 보여줍니다:

```js
import rehypeParse from 'rehype-parse'
import rehypeStringify from 'rehype-stringify'
import {unified} from 'unified'

export const rehype = unified().use(rehypeParse).use(rehypeStringify).freeze()
```

이 프로세서는 다음과 같이 사용하고 구성할 수 있습니다:

```js
import {rehype} from 'rehype'
import rehypeFormat from 'rehype-format'
// …

rehype()
  .use(rehypeFormat)
  // …
```

이와 비슷해 보이는 예제는 동결된 인터페이스에서 작동하기 때문에 오류가 발생합니다.
이러한 동작이 허용된다면 예상치 못한 동작이 발생할 수 있으므로 오류가 발생합니다.
**이것은 유효하지 않습니다**:

```js
import {rehype} from 'rehype'
import rehypeFormat from 'rehype-format'
// …

rehype
  .use(rehypeFormat)
  // …
```

결과:

```txt
~/node_modules/unified/index.js:426
    throw new Error(
    ^

Error: Cannot call `use` on a frozen processor.
Create a new processor first, by calling it: use `processor()` instead of `processor`.
    at assertUnfrozen (~/node_modules/unified/index.js:426:11)
    at Function.use (~/node_modules/unified/index.js:165:5)
    …
```

### `processor.parse(file)`

텍스트를 구문 트리로 파싱합니다.

> 👉 **참고**: `parse`는 아직 *[동결되지 않은][api-freeze]* 경우 프로세서를 동결합니다.

> 👉 **참고**: `parse`는 [파싱 단계][overview]를 수행하며, 실행 단계나 다른 단계는 수행하지 않습니다.

###### 매개변수

* `file` ([`Compatible`][vfile-compatible]) — 파싱할 파일; 일반적으로 `string` 또는 [`VFile`][vfile]; `new VFile(x)`에서 `x`로 받아들이는 모든 값

###### 반환값

`file`을 나타내는 구문 트리 ([`Node`][node]).

###### 예시

이 예제는 `parse`를 사용하여 파일에서 트리를 생성하는 방법을 보여줍니다.

```js
import remarkParse from 'remark-parse'
import {unified} from 'unified'

const tree = unified().use(remarkParse).parse('# Hello world!')

console.log(tree)
```

결과:

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

사용할 파서 ([`Parser`][api-parser], 선택 사항).

### `processor.process(file[, done])`

프로세서에 구성된 대로 주어진 파일을 처리합니다.

> 👉 **참고**: `process`는 아직 *[동결되지 않은][api-freeze]* 경우 프로세서를 동결합니다.

> 👉 **참고**: `process`는 [파싱, 실행, 문자열화 단계][overview]를 수행합니다.

###### 서명

* `processor.process(file, done)`
* `Promise<VFile> = processor.process(file?)`

###### 매개변수

* `file` ([`Compatible`][vfile-compatible], 선택 사항) — 파일; 일반적으로 `string` 또는 [`VFile`][vfile]; `new VFile(x)`에서 `x`로 받아들이는 모든 값
* `done` ([`ProcessCallback`][api-process-callback], 선택 사항) — 콜백

###### 반환값

`done`이 주어진 경우 아무것도 반환하지 않습니다 (`undefined`).
그렇지 않으면 치명적인 오류로 거부되거나 처리된 파일로 해결되는 프로미스 ([`Promise<VFile>`][vfile])를 반환합니다.

파싱, 변환, 컴파일된 값은 `file.value`에서 사용할 수 있습니다 (참고 사항 참조).

> 👉 **참고**: unified는 일반적으로 직렬화하여 컴파일합니다: 대부분의 컴파일러는 `string` (또는 `Uint8Array`)을 반환합니다.
> [`rehype-react`][rehype-react]로 구성된 것과 같은 일부 컴파일러는 다른 값(이 경우 React 트리)을 반환합니다.
> 직렬화하지 않는 컴파일러를 사용하는 경우 다른 결과 값을 예상해야 합니다.
>
> TypeScript에서 사용자 정의 결과를 등록하려면 [`CompileResultMap`][api-compile-result-map]에 추가하세요.

###### 예시

이 예제는 `process`를 사용하여 파일을 처리하는 방법을 보여줍니다:

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

결과:

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

프로세서에 구성된 대로 주어진 파일을 처리합니다.

비동기 변환이 구성된 경우 오류가 발생합니다.

> 👉 **참고**: `processSync`는 아직 *[동결되지 않은][api-freeze]* 경우 프로세서를 동결합니다.

> 👉 **참고**: `processSync`는 [파싱, 실행, 문자열화 단계][overview]를 수행합니다.

###### 매개변수

* `file` ([`Compatible`][vfile-compatible], 선택 사항) — 파일; 일반적으로 `string` 또는 [`VFile`][vfile]; `new VFile(x)`에서 `x`로 받아들이는 모든 값

###### 반환값

처리된 파일 ([`VFile`][vfile]).

파싱, 변환, 컴파일된 값은 `file.value`에서 사용할 수 있습니다 (참고 사항 참조).

> 👉 **참고**: unified는 일반적으로 직렬화하여 컴파일합니다: 대부분의 컴파일러는 `string` (또는 `Uint8Array`)을 반환합니다.
> [`rehype-react`][rehype-react]로 구성된 것과 같은 일부 컴파일러는 다른 값(이 경우 React 트리)을 반환합니다.
> 직렬화하지 않는 컴파일러를 사용하는 경우 다른 결과 값을 예상해야 합니다.
>
> TypeScript에서 사용자 정의 결과를 등록하려면 [`CompileResultMap`][api-compile-result-map]에 추가하세요.

###### 예시

이 예제는 모든 변환이 동기적인 경우 `processSync`를 사용하여 파일을 처리하는 방법을 보여줍니다.

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

결과:

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

구문 트리에 대해 *[변환기][api-transformer]* 를 실행합니다.

> 👉 **참고**: `run`은 아직 *[동결되지 않은][api-freeze]* 경우 프로세서를 동결합니다.

> 👉 **참고**: `run`은 [실행 단계][overview]를 수행하며, 다른 단계는 수행하지 않습니다.

###### 서명

* `processor.run(tree, done)`
* `processor.run(tree, file, done)`
* `Promise<Node> = processor.run(tree, file?)`

###### 매개변수

* `tree` ([`Node`][node]) — 변환하고 검사할 트리
* `file` ([`Compatible`][vfile-compatible], 선택 사항) — `node`와 연관된 파일; `new VFile(x)`에서 `x`로 받아들이는 모든 값
* `done` ([`RunCallback`][api-run-callback], 선택 사항) — 콜백

###### 반환값

`done`이 주어진 경우 아무것도 반환하지 않습니다 (`undefined`).
그렇지 않으면 치명적인 오류로 거부되거나 변환된 트리로 해결되는 프로미스 ([`Promise<Node>`][node])를 반환합닌다..

###### 예시

이 예제는 `run`을 사용하여 트리를 변환하는 방법을 보여줍니다:

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

결과:

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

구문 트리에 대해 *[변환기][api-transformer]*를 실행합니다.

비동기 변환이 구성된 경우 오류가 발생합니다.

> 👉 **참고**: `runSync`는 아직 *[동결되지 않은][api-freeze]* 경우 프로세서를 동결합니다.

> 👉 **참고**: `runSync`는 [실행 단계][overview]를 수행하며, 다른 단계는 수행하지 않습니다.

###### 매개변수

* `tree` ([`Node`][node]) — 변환하고 검사할 트리
* `file` ([`Compatible`][vfile-compatible], 선택 사항) — `node`와 연관된 파일; `new VFile(x)`에서 `x`로 받아들이는 모든 값

###### 반환값

변환된 트리 ([`Node`][node]).

### `processor.stringify(tree[, file])`

구문 트리를 컴파일합니다.

> 👉 **참고**: `stringify`는 아직 *[동결되지 않은][api-freeze]* 경우 프로세서를 동결합니다.

> 👉 **참고**: `stringify`는 [문자열화 단계][overview]를 수행하며, 실행 단계나 다른 단계는 수행하지 않습니다.

###### 매개변수

* `tree` ([`Node`][node]) — 컴파일할 트리
* `file` ([`Compatible`][vfile-compatible], 선택 사항) — `node`와 연관된 파일; `new VFile(x)`에서 `x`로 받아들이는 모든 값

###### 반환값

트리의 텍스트 표현 (`Uint8Array` 또는 `string`, 참고 사항 참조).

> 👉 **참고**: unified는 일반적으로 직렬화하여 컴파일합니다: 대부분의 컴파일러는 `string` (또는 `Uint8Array`)을 반환합니다.
> [`rehype-react`][rehype-react]로 구성된 것과 같은 일부 컴파일러는 다른 값(이 경우 React 트리)을 반환합니다.
> 직렬화하지 않는 컴파일러를 사용하는 경우 다른 결과 값을 예상해야 합니다.
>
> TypeScript에서 사용자 정의 결과를 등록하려면 [`CompileResultMap`][api-compile-result-map]에 추가하세요.

###### 예시

이 예제는 `stringify`를 사용하여 구문 트리를 직렬화하는 방법을 보여줍니다:

```js
import {h} from 'hastscript'
import rehypeStringify from 'rehype-stringify'
import {unified} from 'unified'

const tree = h('h1', 'Hello world!')

const document = unified().use(rehypeStringify).stringify(tree)

console.log(document)
```

결과:

```html
<h1>Hello world!</h1>
```

### `processor.use(plugin[, options])`

플러그인, 사용 가능한 값 목록 또는 프리셋을 사용하도록 프로세서를 구성합니다.

프로세서가 이미 플러그인을 사용하고 있는 경우, 전달된 옵션을 기반으로 이전 플러그인 구성이 변경됩니다.
다시 말해, 플러그인이 두 번째로 추가되지 않습니다.

> 👉 **참고**: `use`는 [*동결된*][api-freeze] 프로세서에서 호출할 수 없습니다.
> 먼저 프로세서를 호출하여 새로운 동결되지 않은 프로세서를 생성하세요.

###### 서명

* `processor.use(preset?)`
* `processor.use(list)`
* `processor.use(plugin[, ...parameters])`

###### 매개변수

* `preset` ([`Preset`][api-preset]) — 플러그인 및 설정
* `list` ([`PluggableList`][api-pluggable-list]) — 사용 가능한 것들의 목록
* `plugin` ([`Plugin`][api-plugin]) — 플러그인
* `parameters` (`Array<unknown>`) — `plugin`에 대한 구성, 일반적으로 단일 옵션 객체

###### 반환값

현재 프로세서 ([`processor`][api-processor]).

###### 예시

`.use()`에 플러그인을 전달하는 방법은 여러 가지가 있습니다.
이 예제는 개요를 제공합니다:

```js
import {unified} from 'unified'

unified()
  // 옵션이 있는 플러그인:
  .use(pluginA, {x: true, y: true})
  // 동일한 플러그인을 다시 전달하면 구성이 병합됩니다 (to `{x: true, y: false, z: true}`):
  .use(pluginA, {y: false, z: true})
  // 플러그인들:
  .use([pluginB, pluginC])
  // 두 개의 플러그인, 두 번째는 옵션과 함께:
  .use([pluginD, [pluginE, {}]])
  // 플러그인과 설정이 있는 프리셋:
  .use({plugins: [pluginF, [pluginG, {}]], settings: {position: false}})
  // 설정만:
  .use({settings: {position: false}})
```

### `CompileResultMap`

컴파일러의 알려진 결과 인터페이스 (TypeScript 타입).

일반적으로 컴파일러는 텍스트를 결과로 반환합니다 (`vfile`의 [`Value`][vfile-value]).
React 노드와 같은 다른 것으로 컴파일할 때 (예: `rehype-react`), 이 인터페이스를 확장하여 해당 타입을 포함할 수 있습니다.

```ts
import type {ReactNode} from 'somewhere'

declare module 'unified' {
  interface CompileResultMap {
    // 새로운 결과 등록 (값이 사용됨, 키는 일치해야 함).
    ReactNode: ReactNode
  }
}

export {} // 이것이 필요하지 않을 수 있지만, 파일이 모듈임을 확인합니다.
```

[`CompileResults`][api-compile-results]를 사용하여 값에 접근하세요.

###### 타입

```ts
interface CompileResultMap {
  // 참고: `VFile`의 `Value`가 변경되면 이것도 변경되어야 합니다.
  Uint8Array: Uint8Array
  string: string
}
```

### `CompileResults`

컴파일러의 허용 가능한 결과 (TypeScript 타입).

사용자 정의 결과를 등록하려면 [`CompileResultMap`][api-compile-result-map]에 추가하세요.

###### 타입

```ts
type CompileResults = CompileResultMap[keyof CompileResultMap]
```

### `Compiler`

**컴파일러**는 구문 트리를 다른 것(대부분의 경우 텍스트)으로 컴파일하는 작업을 처리합니다 (TypeScript 타입).

문자열화 단계에서 사용되며 컴파일할 문서의 [`Node`][node]와 [`VFile`][vfile] 표현과 함께 호출됩니다.
주어진 트리의 텍스트 표현(일반적으로 `string`)을 반환해야 합니다.

> 👉 **참고**: unified는 일반적으로 직렬화를 통해 컴파일합니다: 대부분의 컴파일러는 `string` (또는 `Uint8Array`)을 반환합니다.
> [`rehype-react`][rehype-react]로 구성된 것과 같은 일부 컴파일러는 다른 값(이 경우 React 트리)을 반환합니다.
> 직렬화하지 않는 컴파일러를 사용하는 경우 다른 결과 값을 예상해야 합니다.
>
> TypeScript에서 사용자 정의 결과를 등록하려면 [`CompileResultMap`][api-compile-result-map]에 추가하세요.

###### 타입

```ts
type Compiler
  Tree extends Node = Node,
  Result extends CompileResults = CompileResults
> = (tree: Tree, file: VFile) => Result
```

### `Data`

모든 플러그인에서 지원할 수 있는 알려진 데이터의 인터페이스 (TypeScript 타입).

일반적으로 옵션은 특정 플러그인에 제공될 수 있지만, 때로는 여러 플러그인과 정보를 공유하는 것이 의미가 있습니다.
예를 들어, 모든 단계에서 필요한 self-closing HTML 요소 목록이 있습니다.

이를 타입화하려면 다음과 같이 하세요:

```ts
declare module 'unified' {
  interface Data {
    htmlVoidElements?: Array<string> | undefined
  }
}

export {} // 이것이 필요하지 않을 수 있지만, 파일이 모듈임을 확인합니다.
```

###### 타입

```ts
interface Data {
  settings?: Settings | undefined
}
```

자세한 정보는 [`Settings`][api-settings]를 참조하세요.

### `Parser`

**파서**는 텍스트를 구문 트리로 파싱하는 작업을 처리합니다 (TypeScript 타입).

파싱 단계에서 사용되며 파싱할 문서의 `string`과 [`VFile`][vfile]과 함께 호출됩니다.
주어진 파일의 구문 트리 표현 ([`Node`][node])을 반환해야 합니다.

###### 타입

```ts
type Parser<Tree extends Node = Node> = (document: string, file: VFile) => Tree
```

### `Pluggable`

플러그인과 설정을 추가하는 다양한 방법의 통합 (TypeScript 타입).

###### 타입

```ts
type Pluggable =
  | Plugin<Array<any>, any, any>
  | PluginTuple<Array<any>, any, any>
  | Preset
```

자세한 정보는 [`Plugin`][api-plugin], [`PluginTuple`][api-plugin-tuple],
그리고 [`Preset`][api-preset]을 참조하세요.

### `PluggableList`

플러그인과 프리셋의 목록 (TypeScript 타입).

###### 타입

```ts
type PluggableList = Array<Pluggable>
```

자세한 정보는 [`Pluggable`][api-pluggable]을 참조하세요.

### `Plugin`

단일 플러그인 (TypeScript 타입).

플러그인은 다음과 같은 방식으로 적용되는 프로세서를 구성합니다:

* 파서, 컴파일러 또는 데이터 구성과 같이 프로세서를 변경합니다
* 트리와 파일을 처리하는 방법을 지정합니다

실제로 플러그인은 옵션을 받고 프로세서(`this`)를 구성하는 함수입니다.

> 👉 **참고**: 플러그인은 적용될 때가 아니라 프로세서가 *동결*될 때 호출됩니다.

###### 타입

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

자세한 정보는 [`Transformer`][api-transformer]를 참조하세요.

###### 예시

`move.js`:

```js
/**
 * @import {Plugin} from 'unified'
 */

/**
 * @typedef Options
 *   구성 (필수).
 * @property {string} extname
 *   사용할 파일 확장자 (`.`로 시작해야 함).
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
await write(file) // `example.html`에 작성됩니다.
```

결과:

```txt
example.md: 문제가 발견되지 않았습니다
```

…그리고 `example.html`에:

```html
<h1>Hello, world!</h1>
```

### `PluginTuple`

플러그인과 그 구성의 튜플 (TypeScript 타입).

첫 번째 항목은 플러그인이고, 나머지는 그 매개변수입니다.

###### 타입

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

자세한 정보는 [`Plugin`][api-plugin]을 참조하세요.

### `Preset`

공유 가능한 구성 (TypeScript 타입).

플러그인과 설정을 포함할 수 있습니다.

###### 필드

* `plugins` ([`PluggableList`][api-pluggable-list], 선택적)
  — 플러그인과 프리셋 목록
* `settings` ([`Data`][api-data], 선택적)
  — 파서와 컴파일러를 위한 공유 설정

###### 예시

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

결과:

```txt
example.md: 문제가 발견되지 않았습니다
```

`example.md`는 이제 다음과 같은 내용을 포함합니다:

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

프로세스가 완료되었을 때 호출되는 콜백 (TypeScript 타입).

오류 또는 결과와 함께 호출됩니다.

###### 매개변수

* `error` (`Error`, 선택적)
  — 치명적 오류
* `file` ([`VFile`][vfile], 선택적)
  — 처리된 파일

###### 반환값

없음 (`undefined`).

###### 예시

이 예시는 `process`를 사용하여 콜백과 함께 파일을 처리하는 방법을 보여줍니다.

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

결과:

```txt
문제가 발견되지 않았습니다
```

```markdown
[**@unifiedjs**](https://github.com/unifiedjs)
```

### `Processor`

[`processor`][api-processor]의 타입 (TypeScript 타입).

### `RunCallback`

변환기가 완료되었을 때 호출되는 콜백 (TypeScript 타입).

오류 또는 결과와 함께 호출됩니다.

###### 매개변수

* `error` (`Error`, 선택적)
  — 치명적 오류
* `tree` ([`Node`][node], 선택적)
  — 변환된 트리
* `file` ([`VFile`][vfile], 선택적)
  — 파일

###### 반환값

없음 (`undefined`).

### `Settings`

파서와 컴파일러에서 지원할 수 있는 알려진 추가 옵션의 인터페이스.

이는 사용자가 `remark`와 같은 패키지를 사용할 수 있게 하기 위해 존재합니다. 이러한 패키지는 파서와 컴파일러 모두를 구성합니다(이 경우 `remark-parse`와 `remark-stringify`).

파서나 컴파일러를 만들 때, 함께 패키징될 수 있는 경우, `this.data('settings')`를 입력으로 지원하고 명시적으로 전달된 `options`와 병합해야 합니다.
그런 다음 타입을 지정하려면 `remark-stringify`를 예로 들어 다음과 같이 하세요:

```ts
declare module 'unified' {
  interface Settings {
    bullet: '*' | '+' | '-'
    // …
  }
}

export {} // 이것이 필요하지 않을 수 있지만, 파일이 모듈임을 확인합니다.
```

###### 타입

```ts
interface Settings {}
```

### `TransformCallback`

변환에 전달되는 콜백 (TypeScript 타입).

`transformer`의 서명이 세 번째 인수를 받아들이는 경우, 변환기는 비동기 작업을 수행할 수 있으며 반드시 이를 호출해야 합니다.

###### 매개변수

* `error` (`Error`, 선택적)
  — 프로세스를 중지시킬 치명적 오류
* `tree` ([`Node`][node], 선택적)
  — 새로운, 변경된 트리
* `file` ([`VFile`][vfile], 선택적)
  — 새로운, 변경된 파일

###### 반환값

없음 (`undefined`).

### `Transformer`

변환기는 구문 트리와 파일을 처리합니다 (TypeScript 타입).

이들은 실행 단계에서 구문 트리와 파일이 통과될 때마다 호출되는 함수입니다.
오류가 발생하면(던져지거나, 반환되거나, 거부되거나, `next`에 전달되면) 프로세스가 중지됩니다.

실행 단계는 [`trough`][trough]에 의해 처리되므로, 이러한 함수의 정확한 의미에 대해서는 해당 문서를 참조하세요.

> 👉 **참고**: `next`를 무시해야 할 가능성이 높습니다: 이를 받아들이지 마세요.
> 이는 콜백 스타일의 비동기 작업을 지원합니다.
> 하지만 프로미스가 아마도 이해하기 더 쉬울 것입니다.

###### 타입

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

## 타입

이 패키지는 [TypeScript][]로 완전히 타입화되어 있습니다.
추가적으로 다음과 같은 타입을 내보냅니다:
[`CompileResultMap`][api-compile-result-map],
[`CompileResults`][api-compile-results],
[`Compiler`][api-compiler],
[`Data`][api-data],
[`Parser`][api-parser],
[`Pluggable`][api-pluggable],
[`PluggableList`][api-pluggable-list],
[`Plugin`][api-plugin],
[`PluginTuple`][api-plugin-tuple],
[`Preset`][api-preset],
[`ProcessCallback`][api-process-callback],
[`Processor`][api-processor],
[`RunCallback`][api-run-callback],
[`Settings`][api-settings],
[`TransformCallback`][api-transform-callback],
그리고 [`Transformer`][api-transformer]

TypeScript가 작동하려면 특히 플러그인을 올바르게 타입화하는 것이 중요합니다.
우리는 제네릭과 함께 `Plugin` 타입을 사용하고 우리 패키지에서 제공하는 구문 트리에 대한 노드 타입을 사용하는 것을 강력히 권장합니다 (예를 들어,
[`@types/hast`][types-hast], [`@types/mdast`][types-mdast],
[`@types/nlcst`][types-nlcst]).

```js
/**
 * @import {Root as HastRoot} from 'hast'
 * @import {Root as MdastRoot} from 'mdast'
 * @import {Plugin} from 'unified'
 */

/**
 * @typedef Options
 *   구성 (선택적).
 * @property {boolean | null | undefined} [someField]
 *   일부 옵션 (선택적).
 */

// 옵션을 타입화하려면:
/** @type {Plugin<[(Options | null | undefined)?]>} */
export function myPluginAcceptingOptions(options) {
  const settings = options || {}
  // `settings`는 이제 `Options`입니다.
}

// 특정 트리에서 작동하는 플러그인을 타입화하려면 (옵션 없이):
/** @type {Plugin<[], MdastRoot>} */
export function myRemarkPlugin() {
  return function (tree, file) {
    // `tree`는 `MdastRoot`입니다.
  }
}

// 한 트리를 다른 트리로 변환하는 플러그인을 타입화하려면:
/** @type {Plugin<[], MdastRoot, HastRoot>} */
export function remarkRehype() {
  return function (tree) {
    // `tree`는 `MdastRoot`입니다.
    // 결과는 `HastRoot`여야 합니다.
  }
}

// 파서를 정의하는 플러그인을 타입화하려면:
/** @type {Plugin<[], string, MdastRoot>} */
export function remarkParse(options) {}

// 컴파일러를 정의하는 플러그인을 타입화하려면:
/** @type {Plugin<[], HastRoot, string>} */
export function rehypeStringify(options) {}
```

## 호환성

unified 집단에서 유지 관리하는 프로젝트는 유지 관리되는 Node.js 버전과 호환됩니다.

새로운 주요 릴리스를 할 때, 우리는 유지 관리되지 않는 Node.js 버전에 대한 지원을 중단합니다.
이는 현재 릴리스 라인인 `unified@^11`을 Node.js 16과 호환되도록 유지하려고 노력한다는 것을 의미합니다.

## 기여하기

[`unifiedjs/.github`][health]의 [`contributing.md`][contributing]에서 시작하는 방법을 확인하세요.
도움을 받는 방법은 [`support.md`][support]를 참조하세요.

이 프로젝트에는 [행동 강령][coc]이 있습니다.
이 저장소, 조직 또는 커뮤니티와 상호 작용함으로써 귀하는 이 조건을 준수하는 데 동의하는 것입니다.

보안 보고서를 제출하는 방법에 대한 정보는 [보안 정책][security]을 참조하세요.

## 후원

이 노력을 지원하고 보답하려면 [OpenCollective][collective]에서 후원해주세요!

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
  <a href="https://opencollective.com/unified"><strong>당신도 함께 하시겠습니까?</strong></a>
  <br><br>
</td>
</tr>
</table>

## 감사의 말

unified를 위한 예비 작업은 **[retext][]**를 위해 [2014년에 수행되었으며][preliminary] [`ware`][ware]에서 영감을 받았습니다.
**[remark][]**에서 추가 개발이 진행되었습니다.
프로젝트는 마침내 2015년에 [공개][externalised]되었고 `unified`로 [출시][published]되었습니다.
프로젝트는 **[@wooorm](https://github.com/wooorm)**에 의해 작성되었습니다.

`unified`는 이후 플러그인 아키텍처를 [`trough`][trough]로 이동했지만,
**[@calvinfo](https://github.com/calvinfo)**,
**[@ianstormtaylor](https://github.com/ianstormtaylor)**, 그리고 다른 분들의 [`ware`][ware]에 대한 작업에 감사드립니다.
이는 초기에 큰 영감을 주었습니다.

## 라이선스

[MIT][license] © [Titus Wormer][author]

<!-- 정의 -->

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