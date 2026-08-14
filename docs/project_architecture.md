# EAJRS CrossTalk — Content Architecture

## 1. Overview

EAJRS CrossTalk のウェブサイトは、イベント、議論、事例、参考資料などを継続的に公開・蓄積するための **静的な publication platform** として構築する。

基本方針は以下の通り。

> **Markdown を原稿（source of truth）とし、静的 HTML を公開する。PDF は別コンテンツとして管理せず、公開 HTML から必要に応じて生成する。**

これにより、

- 通常のウェブページとして閲覧できる
- 検索エンジンから発見できる
- URL を使って個々のコンテンツを共有できる
- PC・スマートフォン・支援技術からアクセスしやすい
- GitHub 上で原稿・変更履歴を管理できる
- PDF と HTML の二重管理を避けられる
- サーバーやデータベースを必要としない

という構成を目指す。

---

## 2. Basic Architecture

基本的なコンテンツ生成フローは次のようにする。

```text
Markdown
   │
   │ Static Site Generator
   ▼
HTML
   │
   ├── Web browser
   │
   └── Print CSS
          │
          ▼
       PDF / Print
```

### Source

イベント記録や資料などの本文は Markdown で記述する。

以下を人間が編集する一次データとする。

```text
content/
├── ja/
└── en/
```

### Publication

Markdown から静的 HTML を生成し、GitHub Pages で公開する。

現在の GitHub Pages project site は、

```text
https://eajrs-crosstalk.github.io/home/
```

をベース URL としている。

ルートの `index.html` は現在の bilingual なランディングページとして維持し、長文コンテンツを `/ja/` と `/en/` 以下に配置する。

各コンテンツには可能な限り安定した URL を与える。

例：

```text
/home/ja/activities/2026-kickoff/
/home/en/activities/2026-kickoff/

/home/ja/resources/romanisation/
/home/en/resources/romanisation/
```

### PDF

PDF ファイルをあらかじめ別途作成・保存することは原則として行わない。

HTML に印刷用 CSS (`@media print`) を適用し、ブラウザの印刷機能を利用して PDF を生成できるようにする。

```text
HTML
 ├── Screen CSS
 └── Print CSS
       ↓
    Print / Save as PDF
```

必要になった場合には、将来的に JavaScript による PDF ダウンロード機能を追加することもできる。

---

## 3. Proposed File Structure

公開サイトでは、現在の GitHub Pages の構成を維持し、**repository root の `index.html` をトップページとして残す**。

トップページは現在と同様に日本語・英語の入口として機能させる。

イベント記録や Resources などの長文コンテンツについては、ルート直下の `ja/` および `en/` 以下に配置する。

公開される基本構造は次のようにする。

```text
home/
│
├── index.html                 # トップページ（日英切替）
├── styles.css                 # トップページおよび共通スタイル
├── README.md
│
├── ja/
│   ├── activities/
│   │   ├── index.html         # 日本語 Activities 一覧
│   │   ├── 2026-kickoff/
│   │   │   └── index.html
│   │   ├── 2026-romanisation/
│   │   │   └── index.html
│   │   └── ...
│   │
│   └── resources/
│       ├── index.html         # 日本語 Resources 一覧
│       ├── romanisation/
│       │   └── index.html
│       ├── word-segmentation/
│       │   └── index.html
│       └── ...
│
├── en/
│   ├── activities/
│   │   ├── index.html         # English Activities index
│   │   ├── 2026-kickoff/
│   │   │   └── index.html
│   │   ├── 2026-romanisation/
│   │   │   └── index.html
│   │   └── ...
│   │
│   └── resources/
│       ├── index.html         # English Resources index
│       ├── romanisation/
│       │   └── index.html
│       ├── word-segmentation/
│       │   └── index.html
│       └── ...
│
├── assets/
│   ├── images/
│   ├── icons/
│   └── documents/
│
├── css/
│   └── print.css
│
└── js/
    └── main.js
```

これにより、公開 URL は以下のようになる。

```text
# Top
https://eajrs-crosstalk.github.io/home/

# Activities
https://eajrs-crosstalk.github.io/home/ja/activities/
https://eajrs-crosstalk.github.io/home/en/activities/

# Individual activity
https://eajrs-crosstalk.github.io/home/ja/activities/2026-kickoff/
https://eajrs-crosstalk.github.io/home/en/activities/2026-kickoff/

# Resources
https://eajrs-crosstalk.github.io/home/ja/resources/
https://eajrs-crosstalk.github.io/home/en/resources/

# Individual resource
https://eajrs-crosstalk.github.io/home/ja/resources/romanisation/
https://eajrs-crosstalk.github.io/home/en/resources/romanisation/
```

### Source and Published Files

Markdown を導入した場合、人間が編集する source と、GitHub Pages で公開される HTML は分離して考える。

例えば source は以下のように管理する。

```text
content/
├── ja/
│   ├── activities/
│   │   ├── 2026-kickoff.md
│   │   └── 2026-romanisation.md
│   │
│   └── resources/
│       ├── romanisation.md
│       └── word-segmentation.md
│
└── en/
    ├── activities/
    │   ├── 2026-kickoff.md
    │   └── 2026-romanisation.md
    │
    └── resources/
        ├── romanisation.md
        └── word-segmentation.md
```

Static Site Generator はこれらを公開用 HTML に変換する。

```text
content/ja/activities/2026-kickoff.md
                │
                ▼
        Static Site Generator
                │
                ▼
ja/activities/2026-kickoff/index.html
```

したがって、**source のディレクトリ構造と公開サイトのディレクトリ構造は必ずしも同一である必要はない**。

重要なのは、公開 URL の構造を安定させることである。

### GitHub Pages Base Path

この repository は GitHub Pages の project site として `/home/` 以下で公開される。

そのため、サイト内部でルート相対 URL を使用する際には注意が必要である。

例えば、

```html
<a href="/ja/activities/">
```

とすると、

```text
https://eajrs-crosstalk.github.io/ja/activities/
```

を指すことになり、`/home/` が欠落する。

実際の公開先は、

```text
https://eajrs-crosstalk.github.io/home/ja/activities/
```

である。

そのため Static Site Generator を導入する際には、`/home/` を **base path** として扱える構成にする。

テンプレート内に `/home/` を無数にハードコードすることは避け、base URL / path を設定として一元管理することが望ましい。

---

## 4. Content Types

当面は大きく二種類のコンテンツを想定する。

### Activities

CrossTalk が開催・参加したイベント、ディスカッション、ワークショップなどの記録。

例：

```text
activities/
  2026-kickoff.md
  2026-romanisation.md
```

内容としては、

- Event title
- Date
- Venue / Online
- Participants
- Summary
- Programme
- Presentations
- Discussion
- Outcomes
- References
- Related resources

などを記録できるようにする。

### Resources

CrossTalk の活動から生まれた、継続的に参照可能な情報。

例：

```text
resources/
  romanisation.md
  word-segmentation.md
  metadata-practice.md
```

イベントそのものの記録と、そこから得られた知識・ガイドライン・参考情報を分離する。

---

## 5. Markdown Format

各 Markdown ファイルには Front Matter を付与する。

例：

```markdown
---
title: "EAJRS CrossTalk Kickoff Meeting"
date: 2026-01-15
type: activity
lang: en
translation: /home/ja/activities/2026-kickoff/
---

# EAJRS CrossTalk Kickoff Meeting

## Summary

...

## Programme

...

## Discussion

...

## References

...
```

Front Matter にメタデータを持たせることで、将来的にイベント一覧や Resources 一覧を自動生成できる。

例えば、

```text
title
date
type
lang
translation
authors
contributors
tags
status
```

などを利用できる。

---

## 6. Multilingual Content

トップページのような短い UI については、現在の JavaScript による言語切替を維持できる。

一方、イベント記録や Resources のような長文コンテンツについては、日本語と英語を独立した Markdown 文書として管理する。

```text
content/
├── ja/
│   └── activities/
│       └── 2026-kickoff.md
│
└── en/
    └── activities/
        └── 2026-kickoff.md
```

公開 URL も言語ごとに分離する。

```text
/home/ja/activities/2026-kickoff/
/home/en/activities/2026-kickoff/
```

各ページには対応する翻訳ページへのリンクを表示する。

これにより、

- Markdown の可読性
- 翻訳作業
- URL の明確性
- 検索エンジンによる言語判定
- HTML の `lang` 属性
- 将来的なコンテンツ量の増加

に対応しやすくする。

翻訳が存在しないページについては、無理に空の翻訳ページを作らない。

### トップページから下位ページへのリンク

トップページ (`index.html`) は `data-ja` / `data-en` 属性と JavaScript による言語切替（URL は変わらない）を採用している。一方、Activities・Resources などの下位ページは言語ごとに独立した URL (`ja/activities/`, `en/activities/` など) を持つ。

この2つの方式を橋渡しするため、下位ページへのリンクには `data-href-ja` / `data-href-en` 属性を付与し、既存の `applyLang(lang)` 内でテキスト置換と同様に `href` も書き換える。

```html
<a data-href-ja="ja/activities/" data-href-en="en/activities/" href="ja/activities/">
  Activitiesを見る
</a>
```

- `href` の初期値には日本語版の URL を設定する。JavaScript が無効な環境でも最低限リンクが機能するようにするため。
- 対象は下位ページへ実際に遷移するリンク（hero 内のボタン、Resources カードなど）。トップページ内のセクションへ移動するだけの nav アンカー（`#activities` など）は対象外とし、現状のまま維持する。
- 下位ページの実体がまだ存在しない場合でも、`href` には計画上の正式な URL（本ドキュメント 3章の URL パターンに従うもの）を先に設定してよい。実体ができるまでは 404 になるが、URL 設計とリンクの仕組みを先に確定させることを優先する。

---

## 7. HTML Structure

生成される HTML は、可能な限り semantic HTML を使用する。

例：

```html
<article>

  <header>
    <h1>EAJRS CrossTalk Kickoff Meeting</h1>

    <dl>
      <dt>Date</dt>
      <dd>15 January 2026</dd>

      <dt>Venue</dt>
      <dd>...</dd>
    </dl>
  </header>

  <section id="summary">
    <h2>Summary</h2>
    ...
  </section>

  <section id="programme">
    <h2>Programme</h2>
    ...
  </section>

  <section id="discussion">
    <h2>Discussion</h2>
    ...
  </section>

  <section id="references">
    <h2>References</h2>
    ...
  </section>

</article>
```

見た目だけでなく、文書そのものが意味のある構造を持つことを重視する。

---

## 8. PDF / Print

PDF は HTML と別の出版物として管理するのではなく、HTML の表示形式の一つとして扱う。

基本的には、

```javascript
window.print();
```

を利用する。

ページには例えば、

```text
Print / Save as PDF
```

ボタンを設置する。

印刷時には `print.css` を読み込み、

```css
@media print {

  nav,
  footer,
  .language-switcher,
  .print-button {
    display: none;
  }

  main {
    max-width: none;
  }

  @page {
    size: A4;
    margin: 20mm;
  }

}
```

のように表示を調整する。

これにより、同じ HTML から

```text
Web
Print
PDF
```

の三つの利用形態に対応する。

---

## 9. Static Site Generator

Markdown から HTML を生成するため、Static Site Generator の導入を検討する。

第一候補：

**Eleventy (11ty)**

理由：

- Markdown をそのまま利用できる
- HTML/CSS/JS の知識をそのまま活用できる
- JavaScript ベース
- 構造が比較的単純
- 特定のフロントエンドフレームワークに依存しない
- GitHub Pages と相性が良い
- 小規模な publication site に適している

サイトが大規模な Web application になる予定はないため、React 等のクライアントサイドフレームワークは基本的に必要ない。

Eleventy を導入する場合も、現在のトップページの URL、

```text
https://eajrs-crosstalk.github.io/home/
```

は変更しない。

また `/home/` が GitHub Pages 上の base path であることをビルド設定に反映し、開発環境と GitHub Pages の双方でリンクが正しく機能するようにする。

---

## 10. GitHub Workflow

GitHub repository をコンテンツ管理システムとしても利用する。

基本的な編集フロー：

```text
Create / Edit Markdown
        ↓
     Commit
        ↓
       PR
        ↓
     Review
        ↓
      Merge
        ↓
 GitHub Actions
        ↓
 Eleventy Build
        ↓
  GitHub Pages
```

これにより、

- 誰が何を変更したか
- いつ変更したか
- どの内容がレビューされたか

を Git の履歴として保持できる。

---

## 11. Design Principles

サイト全体では以下を優先する。

### Web first

HTML を主要な公開形式とする。

PDF は補助的な出力形式とする。

### Stable URLs

一度公開したコンテンツの URL は可能な限り変更しない。

特に、

```text
/home/{lang}/{content-type}/{slug}/
```

を基本的な URL パターンとして維持する。

例：

```text
/home/ja/activities/2026-kickoff/
/home/en/resources/romanisation/
```

### Content first

デザインやフレームワークより、公開される情報そのものを中心に設計する。

### Accessibility

semantic HTML を使用し、JavaScript がなくても基本的なコンテンツを閲覧できるようにする。

### Minimal dependencies

可能な限りブラウザ標準機能と静的 HTML/CSS を利用する。

### Single source of truth

同じ内容を HTML / Markdown / PDF として別々に管理しない。

```text
Markdown
   ↓
HTML
   ↓
Print / PDF
```

という一方向の生成を基本とする。

---

## 12. Long-term Direction

この構造にすることで、CrossTalk の活動が増えても、

```text
Activities
   │
   ├── Event
   ├── Event
   ├── Workshop
   └── Discussion
          │
          ▼
Resources
   │
   ├── Romanisation
   ├── Word Segmentation
   ├── Metadata Practice
   └── ...
```

という形で、イベントの記録と、そこから蓄積された知識を継続的に公開できる。

EAJRS CrossTalk のサイトを単なる project homepage ではなく、

> **活動の記録と成果を、オープンかつ持続的に蓄積・公開するためのウェブ出版基盤**

として育てていくことを目標とする。