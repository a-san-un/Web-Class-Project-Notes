# JavaScript-スクロールとIntersectionObserver

## 概要
ページのスクロール量を取得して UI を変化させる方法と、IntersectionObserver を使って要素が画面内に入ったタイミングを検知する方法をまとめたトピック。  
スクロール進捗バーと、スクロール連動アニメーション（一度だけふわっと表示）を中心に整理する。

---

## 1. scrollY とスクロール量の取得

- `window.scrollY`（または `window.pageYOffset`）は、ページが縦方向にどれだけスクロールされたかをピクセル数で返す読み取り専用プロパティ。  
- `window.addEventListener('scroll', ...)` と組み合わせて、スクロールするたびに現在位置を取得できる。

### 例

```js
window.addEventListener('scroll', () => {
    const scrolled = window.scrollY;
    console.log(scrolled);
    });
```

参考：  
- [MDN: Window.scrollY](https://developer.mozilla.org/ja/docs/Web/API/Window/scrollY)

---

## 2. ページ全体に対するスクロール率（0〜100%）

- ページの高さ `document.documentElement.scrollHeight` と、ビューポートの高さ `document.documentElement.clientHeight` の差が「スクロール可能な高さ」。  
- `scrollY / (scrollHeight - clientHeight) * 100` で、ページ全体に対するスクロール率（0〜100）を求められる。

### 例：進捗バー

```js
const bar = document.querySelector('#bar');

function updateScrollPercent() {
    const scrolled = window.scrollY;
    const pageHeight = document.documentElement.scrollHeight;
    const viewHeight = document.documentElement.clientHeight;
    const percent = Math.round((scrolled / (pageHeight - viewHeight)) * 100);
    bar.style.width = `${percent}%`;
}
window.addEventListener('scroll', updateScrollPercent);
```


### よくあるミス
- `scrollHeight` と `clientHeight` を足してしまうなど、式を間違えて 100% を超える値になってしまう。  
- CSS でバーの親要素に `position: fixed;` などを付け忘れて、思った位置に表示されない。

---

## 3. IntersectionObserver の基本

- IntersectionObserver は「ターゲット要素がビューポート（または指定したルート要素）とどのくらい重なっているか」を非同期に監視する API。  
- コールバックには `entries`（IntersectionObserverEntry の配列）と `observer`（自分自身）が渡される。`entry.isIntersecting` で「今交差しているかどうか」を判定できる。

### 例：シンプルな監視

```js
const showBox = (entries) => {
    entries.forEach((entry) => {
        if (entry.isIntersecting) {
            console.log(entry.target, 'が画面に入りました');
        }
    });
};

const observer = new IntersectionObserver(showBox);
const target = document.querySelector('.box');
observer.observe(target);
```


参考：  
- [MDN: IntersectionObserver](https://developer.mozilla.org/ja/docs/Web/API/IntersectionObserver)  
- [MDN: Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)

---

## 4. root / rootMargin / threshold

- `root`：交差を判定する基準となるスクロール領域。通常は `null`（ビューポート）。  
- `rootMargin`：判定領域を外側・内側に広げるマージン（CSS と同じ書き方）。`"0px 0px -20% 0px"` のように指定できる。  
- `threshold`：どれくらい重なったときにコールバックを呼ぶかを 0〜1 の値（または配列）で指定する。

### 例：画面の 50% 入ったら発火

```js
const options = {
    root: null,
    rootMargin: '0px',
    threshold: 0.5,
};

const observer = new IntersectionObserver(showBox, options);
observer.observe(document.querySelector('.box'));
```


### よくあるミス
- threshold を `1` にしていて、「要素が完全に入るまで発火しない」ため思ったよりイベントが起きない。  
- `rootMargin` のマイナス指定を忘れ、期待より遅く発火してしまう。

---

## 5. 一度だけ実行するスクロール連動アニメーション

### 例：ふわっと表示して監視解除

```js
const animateFade = (entries, obs) => {
    entries.forEach((entry) => {
        if (!entry.isIntersecting) return;
        entry.target.animate(
        {
            opacity:,[1]
            filter: ['blur(.4rem)', 'blur(0)'],
            translate: ['0 4rem', 0],
        },
        {
            duration: 2000,
            easing: 'ease',
            fill: 'forwards',
        }
);

// 一度アニメーションしたら監視解除
obs.unobserve(entry.target);

});
};

const fadeObserver = new IntersectionObserver(animateFade);
const fadeElements = document.querySelectorAll('.fadein');

fadeElements.forEach((el) => {
    fadeObserver.observe(el);
});
```

- `.fadein` クラスには CSS で `opacity: 0;` を指定しておき、`fill: 'forwards'` でアニメーション後の状態（opacity:1）を維持する。  
- `obs.unobserve(entry.target)` を呼ぶことで、同じ要素に対して何度もアニメーションが走るのを防いでいる。

参考：  
- [MDN: IntersectionObserverEntry](https://developer.mozilla.org/ja/docs/Web/API/IntersectionObserverEntry)  
- [MDN: IntersectionObserverEntry.isIntersecting](https://developer.mozilla.org/ja/docs/Web/API/IntersectionObserverEntry/isIntersecting)

---

## 6. IntersectionObserver と scroll イベントの使い分け

- `scroll` イベント：スクロールのたびに呼ばれるので、スクロール位置をリアルタイムに使いたいプログレスバーなどに向いている。処理が重いとパフォーマンスに影響しやすい。  
- IntersectionObserver：特定の要素が「画面に入った／出た」瞬間だけ知りたいときに向いている。ブラウザ側で最適化されているため、複数要素でも負荷が少ない。

### よくあるミス
- スクロール連動アニメーションをすべて `scroll` イベントで書いてしまい、毎フレーム判定するようなコードになって重くなる。  
- IntersectionObserver だけでスクロール率まで求めようとする（スクロール位置そのものが必要な場合は `scrollY` を使う）。

---

## 関連デイリーノート

- [JavaScript/2025-12-01.md](../2025-12-01.md)（scrollY とスクロール進捗バーの計算）  
- [JavaScript/2025-12-03.md](../2025-12-03.md)（IntersectionObserver の基本と options、複数アニメーションの実装）  
- [JavaScript/2025-12-04.md](../2025-12-04.md)（キリン画像の一度だけアニメーション、rootMargin / threshold の調整）  
- [JavaScript/2025-12-10.md](../2025-12-10.md)（`.fadein` 要素のふわっと表示と `unobserve` パターン）



