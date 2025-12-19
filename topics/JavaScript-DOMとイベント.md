# JavaScript-DOMとイベント

## 概要
HTML 文書を「DOM ツリー」として操作する方法と、ユーザーの操作やページ読み込みに反応するイベント処理をまとめたトピック。  
要素の取得・書き換え・クラス操作・スタイル変更と、`addEventListener` によるイベント登録を整理する。

---

## 1. DOM とは何か

- ブラウザは HTML を読み込むと、要素をノードの木構造（DOMツリー）としてメモリ上に表現する。JavaScript はこの DOM を通して文書の構造・内容・見た目を操作する。  
- DOM の入口は `document` オブジェクトで、そこから `querySelector` などを使って要素ノード（`Element` / `HTMLElement`）を取得していく。  

参考：  
- [MDN: Document Object Model (DOM)](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)  
- [MDN: DOM scripting introduction](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/DOM_scripting)

---

## 2. 要素の取得とテキスト操作

### 主なメソッド
- `document.querySelector('セレクタ')`：最初に一致した 1 要素を取得。  
- `document.querySelectorAll('セレクタ')`：一致した複数要素を `NodeList` として取得。  
- `element.textContent`：要素内のテキストを取得・書き換え。フォーム要素は `value` プロパティもよく使う。  

### 例

```js
const title = document.querySelector('h1');
title.textContent = 'Hello DOM';

const items = document.querySelectorAll('.item');
items[0].textContent = '1つ目を書き換え';
// もしくは
// items.forEach((item, index) => {
//   item.textContent = `Item ${index + 1}`;
// });
```

### よくあるミス
- セレクタの書き間違い（クラス名なのに `'.class'` の `.` を忘れるなど）で `null` が返り、`null.textContent` でエラーになる。  
  → `console.log` で一度中身を確認してからプロパティにアクセスする。  
- `textContent` と `innerHTML` を混同し、HTML 文字列を入れたのにタグがそのまま表示される。  

---

## 3. クラス操作とスタイル変更

### クラス操作（classList）
- `element.classList.add('class')` / `remove('class')` / `toggle('class')` / `contains('class')` でクラスの付け外しができる。  
- ダークテーマ切り替え・ローディング画面の表示/非表示など、「状態によって見た目を変える」処理に使う。  

```js
const body = document.body;
const btn = document.querySelector('#btn');

btn.addEventListener('click', () => {
body.classList.toggle('dark-theme');
});
```

### スタイル変更（style）
- インラインスタイルは `element.style.プロパティ名 = 値` で変更できる。CSS の `background-color` は `backgroundColor` のようにキャメルケースで書く。  

```js
const text = document.querySelector('\#text');
text.style.backgroundColor = 'yellow';
text.style.fontSize = '24px';
```

### よくあるミス
- CSS プロパティ名をそのまま使って `style['background-color']` と書きたくなる。  
  → 通常は `style.backgroundColor` を使う（ハイフン区切りはキャメルケースに変換）。  
- 表示/非表示を CSS 側で `display: none;` にしているのに、JS で `opacity` だけ変えても見えない。  

---

## 4. よく使うイベントと addEventListener

### 主なイベント
- クリック・入力系：`click`, `input`, `change`, `keyup`, `submit` など。  
- ページ系：`DOMContentLoaded`, `load`, `scroll`, `resize` など。  

```js
const button = document.querySelector('#btn');
button.addEventListener('click', (event) => {
  console.log('クリックされました', event.target);
});
```

### DOMContentLoaded と load
- `DOMContentLoaded`：HTML の構文解析と defer スクリプトの実行が終わり、DOM 操作ができるタイミングで発火（画像などの読み込みは待たない）。  
- `load`：画像・CSS などの外部リソースも含めてページ全体が読み込み完了したときに発火する。  

```js
document.addEventListener('DOMContentLoaded', () => {
console.log('DOM が準備完了');
});

window.addEventListener('load', () => {
console.log('ページの読み込みがすべて完了');
});
```

### よくあるミス
- DOM 要素より前でスクリプトを読み込んでいるのに、`DOMContentLoaded` を使わず `querySelector` しようとして `null` になる。  
- `onclick` 属性や `element.onclick = ...` と `addEventListener` を混在させ、後から代入した方だけが動作してしまう。`addEventListener` に統一した方が安全。  

参考：  
- [MDN: addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)  
- [MDN: DOMContentLoaded イベント](https://developer.mozilla.org/ja/docs/Web/API/Document/DOMContentLoaded_event)

---

## 5. 入力フォームと状態管理

### 例：文字数カウント

```js
const textarea = document.querySelector('\#text');
const count = document.querySelector('\#count');

textarea.addEventListener('keyup', () => {
const length = textarea.value.length;
count.textContent = length;

count.classList.toggle('alert', length > 100);
});
```

- `value` で入力値を取得し、`length` で文字数を数える。  
- 条件に応じてクラス `alert` を付け外しし、CSS で色を変えるなどのフィードバックを行う。  

### 例：チェックボックスとボタンの活性/非活性

```js
const agree = document.querySelector('\#agree');
const submitBtn = document.querySelector('\#submit');

agree.addEventListener('change', () => {
submitBtn.disabled = !agree.checked;
});
```

- チェックボックスの `checked` プロパティが `true` のときだけボタンを有効にする。  
- `disabled` は boolean プロパティなので、`true/false` を直接代入すると読みやすい。  

---

## 6. ページ読み込みとローディング演出

- ローディング用要素を `loading` クラスで表示しておき、`window.addEventListener('load', ...)` 内で `classList.add('loaded')` することで、フェードアウトさせるパターンを学んだ。  
- CSS 側では `.loaded` に `opacity: 0; visibility: hidden; transition: all 1s;` などを設定しておく。  

```js
const loading = document.querySelector('\#loading');

window.addEventListener('load', () => {
loading.classList.add('loaded');
});
```

### よくあるミス
- `DOMContentLoaded` で画像のサイズを測ろうとして、まだ画像が読み込まれておらず値がおかしくなる。画像や外部リソース依存の処理は `load` で行う。  

---

## 関連デイリーノート

- [JavaScript/2025-11-27.md](../2025-11-27.md)（CSS と JavaScript の連携、classList、DOMContentLoaded と load）  
- [JavaScript/2025-11-28.md](../2025-11-28.md)（ダークテーマ切り替え、文字数カウンター、チェックボックスとボタン）  
- [JavaScript/2025-12-01.md](../2025-12-01.md)（scroll イベントと進捗バーの更新）  
- [JavaScript/2025-12-08.md](../2025-12-08.md)（ギャラリーのサムネイル hover、`dataset` と `NodeList.forEach`）  
- [JavaScript/2025-12-09.md](../2025-12-09.md)（モーダル・メニューの開閉イベント）  
- [JavaScript/2025-12-10.md](../2025-12-10.md)（IntersectionObserver のコールバックとイベントの考え方の復習）