---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# Composablesについて

---

# Agenda
- Vueで頻出する概念についておさらい
- Composablesの機能深掘り
- Vue2との比較
- ぐっちさんの質問コーナー

---

# その前に自己紹介...

---

# 橘井 貴輝 (きっちゃん)

<div style="display: flex">
  <ul>
    <li>変遷: SPK → TYO → Darkside OYC → TYO</li>
    <li>好きなキャラクター: コバトン、ポコニャン、山岡士郎</li>
  </ul>
  <img src="/F0Pzp6oB_400x400.jpeg" style="height: 150px; margin-left: 170px">
</div>

<div>
  <h3>最近あったこと</h3>
  <ul>
    <li>情緒を感じられた（桜を見て）</li>
    <li>
      2010年代以降の日本映画（高校生青春もの中心）に見ていく
      <ul>
        <li>見ていて辛くなる（古澤健👍👍、松本花奈👍）</li>
        <li>『明治侠客伝 三代目襲名』を見て出直して欲しい</li>
      </ul>
    </li>
    <li>音楽: Zetton👍、Volcano👍、Dregs One & Ill Sugi👍、Budamunk & J.Lamottaすずめ👍、iblss👍</li>
  </ul>
</div>

---

# Lifecycle Hooksについておさらい
- Vueコンポーネントインスタンスは作成時に一連の初期化手順を実行する
  - データ監視の設定
  - テンプレートのコンパイル
  - インスタンスをDOMへマウント
  - データ変更が生じた際のDOM更新
- これらの過程でLifecycle Hooksと呼ばれる関数が実行され、<br />ユーザーは特定の段階で独自のコードを追加できる

---

# Lifecycle Hooksについておさらい
例えば、`onMounted`はコンポーネントが初期レンダリングを終えて、<br/>
DOMノードを作成した際に走ってくれる

```js
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```

---

# Lifecycle Hooksについておさらい
- `onMounted`, `onUpdated`, `onUnmounted`がVue3で頻出！
- VueはこれらのHooksに登録されたコールバック関数をアクティブなコンポーネントと自動的に関連付けする
- Hooksはコンポーネントのセットアップ時に同期的に登録する必要がある
- `setup()`内で使用されていれば、Hooksを外部関数として呼び出すこともできる
- ライフサイクルの流れは[こちら](https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram)

```js
setTimeout(() => {
  onMounted(() => {
    // this won't work.
  })
}, 100)
```

---

# Reactivityとは？

> One of Vue’s most distinct features is the unobtrusive reactivity system.

出典: [Reactivity in Depth](https://vuejs.org/guide/extras/reactivity-in-depth.html#reactivity-in-depth)

- VueのコンポーネントはリアクティブなJavaScriptのオブジェクトである
- 中身が変更されると、ビューが更新される

---

# Reactivityとは？

- 宣言的に変化に対応できるようにするプログラミングパラダイム

<video controls="controls">
  <source src="/reactivity-demo.mov">
</video>

---

# Reactivityとは？

- これをJavaScriptで書こうとすると...🤔
- 値をupdate（side effect、この例では足し算）してくれる関数を用意する必要がある
- さらにA0やA1など依存関係の変更を検知してくれる関数も必要

```js
let A0 = 1
let A1 = 2
let A2 = A0 + A1

console.log(A2) // 3

A0 = 2
console.log(A2) // Still 3
```

---

# VueにおけるReactivityとは？

- JavaScriptではローカル変数の読み書きを追跡する仕組みはない
- ただし、オブジェクトのプロパティの読み書きは追跡できる
  - 方法としては`getter/setter` or `Proxies`
  - Vue2ではブラウザのサポート問題のせいで`getter/setter`を主に利用
  - Vue3からは`reactive`オブジェクトが`Proxies`、 `ref`が`getter/setter`に対応

---

# VueにおけるReactivityとは？

- いくつか制限もある
  - `reactive`オブジェクトのプロパティをローカル変数へ代入or分割代入すると、リアクティブ性が無くなる
  - `reactive`オブジェクトとオリジナルのオブジェクトは`===`演算子で比較できない
- RxJS（非同期イベントストリーム）とVueUseを合わせてより高度な使い方もできる

---

# Composablesとは？

- Vueの文脈では、Composition APIを活用して`statefull`なロジックをカプセル化し、再利用する機能のことを指す
- 例えば、date-fnsで日付をフォーマットしてコンポーネントで使いまわしたい！
  - ある入力を受け付けて、期待通りの出力をすぐに返す
  - このようなものを`stateless`なロジックと呼んでいる
- 一方、時間と共に変化をする状態を管理するものを`statefull`なロジックと定義している
  - ページ上のマウスの現在位置を追跡するようなもの
  - 実際にはデータベースへの接続状態を監視したりなど、より複雑なロジックも含まれる
  
---

# Composablesとは？

ロジックの切り出し

```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)
  
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }
  
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))
  
  return { x, y }
}
```

---

# Composablesとは？

使う側

```js
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

---

# 非同期を扱う例

ロジックの切り出し

```js
// fetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

---

# 非同期を扱う例

使う側

```js
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

---

# 非同期を扱う例

- さっきの例だと静的なURL文字列を受け取った1度だけで終わってしまう
- URLが変更されるたびに再取得するにはどうすれば良い？🤔

---

# 非同期を扱う例

- 引数として`ref`を受け取って変更を検知できるようにすればOK🚀

---

# 非同期を扱う例

```js
import { ref, isRef, unref, watchEffect } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  function doFetch() {
    data.value = null
    error.value = null
    // unref() unwraps potential refs
    fetch(unref(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  if (isRef(url)) {
    watchEffect(doFetch)
  } else {
    doFetch()
  }

  return { data, error }
}
```

---

# 慣例やベストプラクティス

- Composablesな関数には"use"で始まるキャメルケースの命名が慣例
- リアクティブ性に依存していない場合でも、refの値を受け付けられる

```js
import { unref } from 'vue'

function useFeature(maybeRef) {
  // Composablesで利用する引数がrefか否か判別しづらい
  // Ref<string> or string...
  // 必要なければunref()で解除しちゃうこともできる
  const value = unref(maybeRef)
}
```

- `reactive()`ではなく`ref()`を使う

---

# 慣例やベストプラクティス

- Side Effects
  - Composablesな関数の中で副作用を扱うことは問題ないが以下のルールに注意
    - SSRを利用する場合は、`onMounted()`でDOM固有の副作用を必ず実行する
    - `onUnmounted()`で副作用をクリーンアップする必要がある
- `<script setup>`構文もしくは`setup()`内で使う
  - Vueがアクティブなコンポーネントインスタンスを決定できるコンテクスト（環境、文脈）のため
  - ライフサイクルを登録したり、アンマウント時に廃棄する`computed`、`watcher`要素を決定できるため

---

# コードの整理のために利用する

- 再利用するためにロジックを切り出す以外に、単純にコードの見通しを良くすることもできる
- ここら辺はチームごとのルール整備などが必要？

```js
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```

---

# Vue2機能との比較

## vs. Mixins

- Mixinsって何だっけ？
  - Vue2でコンポーネントやロジックを再利用可能なユニットに抽出できる機能
  - Vue3でもサポートされているがComposition API推奨

---

# Vue2機能との比較

## vs. Mixins

```js
// mixinオブジェクトを定義
const mixin = {
	// lifecycleフックを定義する
  created() {
    console.log(1)
  }
}

createApp({
  created() {
    console.log(2)
  },
  mixins: [mixin] // mixinsはオブジェクトの配列を受け取る
})

// コンポーネント側のフックより先にmixinsが呼び出される
// => 1
// => 2
```

---

# Vue2機能との比較

## vs. Mixins

Mixinsの弱点
- プロパティのソースが不明確
  - 多くのmixinsを使う場合、どのインスタンスのプロパティがどのmixinsから来ているか分りづらい
- 名前空間の衝突
  - 異なる複数のmixinsが同じプロパティキーで登録されている可能性がある
  - Composablesでは異なるComposablseからのキーが衝突する場合、よしなにやってくれる
- 暗黙のcross-mixinの通信
  - 互いに相互作用する必要がある場合に、密結合になってしまう
  - Composablesの場合、あるComposablesから返ってきた値を、通常の関数と同じように、別のComposablesにも引数として渡せる

---

# Vue2機能との比較

## vs. Renderless Components

- Renderless Componentsって何だ？
  - Slotの章で出てくる
  - ロジックだけをカプセル化し、それ自身では何もレンダリングしないコンポーネントのこと
  - コンポーネントの入れ子に伴うパフォーマンスの悪化に繋がる
  - 単純にロジックを再利用する場合はComposables、ロジックとビジュアルレイアウトの両方を再利用する場合はRenderless Componentsが良いらしい
- [Renderless Componentsの例](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCBNb3VzZVRyYWNrZXIgZnJvbSAnLi9Nb3VzZVRyYWNrZXIudnVlJ1xuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cblx0PE1vdXNlVHJhY2tlciB2LXNsb3Q9XCJ7IHgsIHkgfVwiPlxuICBcdE1vdXNlIGlzIGF0OiB7eyB4IH19LCB7eyB5IH19XG5cdDwvTW91c2VUcmFja2VyPlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59IiwiTW91c2VUcmFja2VyLnZ1ZSI6IjxzY3JpcHQgc2V0dXA+XG5pbXBvcnQgeyByZWYsIG9uTW91bnRlZCwgb25Vbm1vdW50ZWQgfSBmcm9tICd2dWUnXG4gIFxuY29uc3QgeCA9IHJlZigwKVxuY29uc3QgeSA9IHJlZigwKVxuXG5jb25zdCB1cGRhdGUgPSBlID0+IHtcbiAgeC52YWx1ZSA9IGUucGFnZVhcbiAgeS52YWx1ZSA9IGUucGFnZVlcbn1cblxub25Nb3VudGVkKCgpID0+IHdpbmRvdy5hZGRFdmVudExpc3RlbmVyKCdtb3VzZW1vdmUnLCB1cGRhdGUpKVxub25Vbm1vdW50ZWQoKCkgPT4gd2luZG93LnJlbW92ZUV2ZW50TGlzdGVuZXIoJ21vdXNlbW92ZScsIHVwZGF0ZSkpXG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8c2xvdCA6eD1cInhcIiA6eT1cInlcIi8+XG48L3RlbXBsYXRlPiJ9)

---

# 軽くまとめ

- ロジックを細かく切り出して再利用できる🚀
- ComposablesはVue2の良くなかった機能を改良してくれたもの👍
- ライフサイクルの仕組み、Vueのリアクティブ性への理解が不可欠🔄

---

# 次回予告

- React Hooksとの違い
- Composablesのテストについて