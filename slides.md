---
# try also 'default' to start simple
theme: geist
class: "text-center"
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
layout: image
image: /book.jpg
---

<!-- # テストに用いる Storybook -->
<div class="relative h-full"> 
  <div class="absolute inset-0 flex items-center">
    <h1 class="m-0 pl-4">テストに用いるStorybook</h1>
  </div>
</div>

---

<Toc/>

---

<h1>自己紹介</h1>
<div class="flex gap-20">
  <img src="/profile.jpg" class="mt-10 h-60 rounded shadow" />
  <div>
    <h2>
      小張 泰志(こばり たいし)
      <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
      class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-github />
      </a>
    </h2>
    <h3>・21卒</h3>
    <h3>・フロントエンド</h3>
    <h2>最近熱くなったこと</h2>
    <h3>・WBC優勝</h3>
  </div>
</div>

<!--
~00:30
-->

---
layout: two-cols
---

# Storybookとは

## UIコンポーネントを可視化するツール
### ・デザイナー、PdMとの共有
<v-click>
  <h3>・テストができる←今日話すこと</h3>
</v-click>


::right::

<div class="flex items-center h-full">
  <img src="/storybook.png" class="object-contain rounded shadow" />
</div>

<style>
h1 {
  color: #FE4785
}
</style>

<!--
~01:00
-->

---
layout: two-cols
---

# play functionを使う

```tsx {all|1,4-16}
// ReportFiltersCheckbox.stories.tsx
export const UncheckAll = {
  name: '全てのチェックを外せること',
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    // 全てのチェックを外す
    await userEvent.click(
      canvas.getByRole('checkbox', { name: 'パブリシティ' }),
    );
    await userEvent.click(
      canvas.getByRole('checkbox', { name: 'パブリシティ転載' }),
    );
    await userEvent.click(
      canvas.getByRole('checkbox', { name: 'リリース原文転載' }),
    );
  },
} satisfies Story;

```

::right::

<div class="flex items-center h-full">
  <img src="/play-function.png" class="object-contain rounded shadow" />
</div>

<style>
.slidev-layout {
  gap: 8px
}
</style>

<!--
画面を見せる

https://storybook-hosting-bucket.s3.ap-northeast-1.amazonaws.com/clipping/main/index.html?path=/story/components-reportfilters-reportfilterscheckbox--uncheck-all

playにユーザーを書く

~03:00
-->

---
layout: two-cols
---

<style>
.col-right {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
</style>


# Storyをテストファイルに読み込む
<h2>assertは<img src="https://vitest.dev/logo-shadow.svg" class="h-8 inline-block rounded shadow" />
vitestで行う</h2>

### ・Storybookをbuildせずに実行できるので速い

### ・mockが使える

::right::

```tsx {all|1,10|1,11-17}
// ReportFiltersCheckbox.test.tsx
import { composeStories } from '@storybook/react';
import { render } from '@testing-library/react';
import * as stories from './ReportFiltersCheckbox.stories';

const { UncheckAll } = composeStories(stories);

it('全てのチェックを外せること', async () => {
  const { container, getByRole } = render(<UncheckAll />);
  await UncheckAll.play({ canvasElement: container });
  expect(getByRole('checkbox', { name: 'パブリシティ' })).not.toBeChecked();
  expect(
    getByRole('checkbox', { name: 'パブリシティ転載' }),
  ).not.toBeChecked();
  expect(
    getByRole('checkbox', { name: 'リリース原文転載' }),
  ).not.toBeChecked();
});
```

<!--
act = play

assert = expect

~04:00
-->

---

# 導入した目的とそのギャップ

## 1. play functionによるテストの可視化
<h3 class="color-green-500">達成できた</h3>

## 2. Figmaプラグインの導入
実装とFigmaのズレを減らしたかった

Chromaticを導入しないといけなかったので断念

## 3. VRT
### 今後進めていきたい

<style>
h1 {
  margin-top: 1rem
}
</style>

<!--
Figmaプラグインの話
- Figma上でStorybookを確認できる
VRT
~04:30
-->

---

# 意外な恩恵

## ・ユニットテストのデバッグが楽になった

## ・AAA(Arrange, Act, Assert)を意識するようになった
Assertを独立して書くので、テストの意図が明確になった。

Assert後にActするようなパターンも書きづらくなった。

<!--
デバッグが楽になった

今まではブラックボックスだったjest/vitestのテスト
他の人が書いたテストのデバッグ

ユニットテストはassertが1つであるのが望ましい
integrationテストとの分離

~05:00
-->

---

# 今後の課題
## データ取得するコンポーネントのStorybook化
mswなどを使ってmockできる部分とそうでない部分がある

## どこまでを Storybook に書くのか

・境界があいまい

・すべてを Storybook に寄せることは不可能

・mockがStorybookに導入されたら？


> We have a long list of quality of life improvements here that we’ll be rolling out in 7.x, especially around better mocking, full page testing, and compatibility.[^1]

[^1]: [Storybook 7.0](https://storybook.js.org/blog/storybook-7-0/)


<style>
h1 {
  margin-top: 1rem
}
</style>

<!--
mswを使っているが、recoilなどライブラリの噛み合わせが悪い場合もある

assertをStorybookに書かないのに利点はあるが、理由はない

~06:00
-->

---

# 今までのStorybookの使い方と比較して

<h2><span class="color-pink-500">今まで</span>：実装者以外とのコミュニケーションとしてのツール</h2>

### ただ、、、
デザイナーは figma を見るし、QA は staging を見る

エンジニアもそんなに Storybook を見ない

<h2 class="color-green-500">テストツールとして活用後</h2>

### エンジニアがStorybookを見る機会が増えた
・バックエンドができるまでの開発

・テストの説明

<style>
h1 {
  margin-top: 1rem
}
</style>

<!--
Storybookの立ち位置が変わった話

~07:00
-->

---

# まとめ
## Storybookをテストに活用して、テストが可視化された
## まだ道半ば🛣️
### ・VRTの導入
### ・Storybookのアップデートにも注視していきたい

<!--
~07:30
-->

---
layout: center
---

# ご清聴ありがとうございました✨
