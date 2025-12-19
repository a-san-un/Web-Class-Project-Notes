# JavaScript-WebAnimationsAPIとアニメーション

## 概要
CSS の transition / animation と、JavaScript から直接アニメーションを制御できる Web Animations API（`element.animate`）を整理したトピック。  
ローディング画面・ヒーローエリア・モーダル・メニュー・ギャラリーなどで使ったパターンをまとめる。

---

## 1. CSS アニメーションと transition のおさらい

- CSS の `transition` は「ある状態から別の状態への変化」を滑らかにする仕組みで、`:hover` やクラスの付け替えと組み合わせて使う。  
- `opacity`, `visibility`, `transform`, `background-color` などを CSS クラスの切り替え（`classList.add/remove/toggle`）と合わせてアニメーションさせる。  

### 例：ローディング画面のフェードアウト

```css
#loading {
opacity: 1;
visibility: visible;
transition: opacity 1s, visibility 1s;
}

#loading.loaded {
opacity: 0;
visibility: hidden;
}
```
undefined

```js
const loading = document.querySelector('#loading');
window.addEventListener('load', () => {
  loading.classList.add('loaded');
});
```

---

## 2. Web Animations API の基本（element.animate）

- `element.animate(keyframes, options)` でアニメーションを開始できる。戻り値は `Animation` オブジェクト。  
- `keyframes` は `{ opacity: [0, 1] }` などのオブジェクト、`options` は `duration`, `delay`, `easing`, `fill`, `iterations` などを指定するオブジェクト。

### 例：タイトルのフェードイン

```js
const heading = document.querySelector('.hero-title');

const keyframes = {
  opacity: [0, 1],
  translate: ['0 2rem', 0],
};

const options = {
  duration: 2000,
  easing: 'ease',
  fill: 'forwards',
};

heading.animate(keyframes, options);
```

参考：  
- [MDN: Element.animate](https://developer.mozilla.org/ja/docs/Web/API/Element/animate)  
- [MDN: Web Animations API](https://developer.mozilla.org/ja/docs/Web/API/Web_Animations_API)

---

## 3. ローディング画面とスクリーン遷移

### 例：2枚のローディングレイヤー

```js
const loadingGrey = document.querySelector('#loading');
const loadingGreen = document.querySelector('#loading-screen');

window.addEventListener('load', () => {
  loadingGrey.animate(
    {
      opacity: [1, 0],
      background: ['rgba(238,221,136,1)', 'rgba(238,221,136,0)'],
      visibility: ['visible', 'hidden'],
    },
    {
      duration: 2000,
      delay: 1200,
      easing: 'ease',
      fill: 'forwards',
    }
  );

  loadingGreen.animate(
    {
      translate: ['0 100vh', '0 0', '0 -100vh'],
    },
    {
      duration: 2000,
      delay: 800,
      easing: 'ease',
      fill: 'forwards',
    }
  );
});
```


- 複数のレイヤーに別々の `animate` を掛けることで、画面全体がスライドして開くような演出を実現している。  
- `fill: 'forwards'` により、アニメーション終了後の状態を保持し、途中の状態に戻らないようにしている。

---

## 4. メニュー・モーダルの開閉アニメーション

### メニューのスライドイン

```js
const menuPanel = document.querySelector('.menu-panel');
const menuOpen = document.querySelector('#menu-open');
const menuClose = document.querySelector('#menu-close');
const menuItems = document.querySelectorAll('.menu-panel li');

menuOpen.addEventListener('click', () => {
  menuPanel.animate(
    { translate: ['100vw', '0'] },
    { duration: 400, easing: 'ease', fill: 'forwards' }
  );

  menuItems.forEach((item, index) => {
    item.animate(
      {
        opacity: [0, 1],
        translate: ['2rem', '0'],
      },
      {
        duration: 400,
        delay: 300 * index,
        easing: 'ease',
        fill: 'forwards',
      }
    );
  });
});

menuClose.addEventListener('click', () => {
  menuPanel.animate(
    { translate: ['0', '100vw'] },
    { duration: 400, easing: 'ease', fill: 'forwards' }
  );

  menuItems.forEach((item) => {
    item.animate(
      { opacity: [1, 0] },
      { duration: 200, easing: 'ease', fill: 'forwards' }
    );
  });
});

```

### モーダルとマスク

```js
const modal = document.querySelector('#modal');
const mask = document.querySelector('#mask');
const close = document.querySelector('#close');

const showKeyframes = {
  opacity: [0, 1],
  visibility: ['hidden', 'visible'],
};

const hideKeyframes = {
  opacity: [1, 0],
  visibility: ['visible', 'hidden'],
};

const options = {
  duration: 800,
  easing: 'ease',
  fill: 'forwards',
};

close.addEventListener('click', () => {
  modal.animate(hideKeyframes, options);
  mask.animate(hideKeyframes, options);
});

mask.addEventListener('click', () => {
  close.click();
});
```


- モーダル本体とマスクを同じ keyframes / options でアニメーションさせることで、一体感のある開閉演出になる。  

---

## 5. ギャラリーとサムネイルのアニメーション

### サムネイル hover でメイン画像切り替え

```js
const mainImage = document.querySelector('.gallery-image img');
const thumbImages = document.querySelectorAll('.gallery-thumbnails img');

thumbImages.forEach((thumb) => {
  thumb.addEventListener('mouseover', (e) => {
    const captionText = `${e.target.alt} ${e.target.dataset.extra || ''}`;
    document.querySelector('#caption').textContent = captionText;

    mainImage.src = e.target.src;
    mainImage.animate(
      { opacity: [0, 1] },
      { duration: 500, easing: 'ease', fill: 'forwards' }
    );
  });
});
```


- `NodeList.forEach` で複数のサムネイルに同じ処理を付け、`dataset` から追加情報（色名など）をキャプションとして表示している。  
- メイン画像の変更時に `opacity` アニメーションを掛けることで、切り替わりが自然になる。

参考：  
- [MDN: NodeList.forEach](https://developer.mozilla.org/ja/docs/Web/API/NodeList/forEach)  
- [MDN: HTMLElement.dataset](https://developer.mozilla.org/ja/docs/Web/API/HTMLElement/dataset)

---

## 6. Animation オブジェクトでの制御（応用）

- `const anim = element.animate(...);` とすると、返ってきた `anim` に対して `play()`, `pause()`, `reverse()`, `finish()` などのメソッドを呼び出せる。  
- スクロール量やクリック回数によってアニメーションを一時停止・再生するなど、インタラクティブな制御も可能。

### 例（パターンだけ）

```js
const box = document.querySelector('.box');
const anim = box.animate(
  { translate: ['0 0', '200px 0'] },
  { duration: 1000, fill: 'forwards' }
);

document.querySelector('#pause').addEventListener('click', () => {
  anim.pause();
});

document.querySelector('#play').addEventListener('click', () => {
  anim.play();
});
```


---

## 関連デイリーノート

- [JavaScript/2025-11-27.md](../2025-11-27.md)（CSS transition でのローディング画面・opacity/visibility の切り替え）  
- [JavaScript/2025-12-02.md](../2025-12-02.md)（見出しの `element.animate`、Math ランダムと組み合わせた演出）  
- [JavaScript/2025-12-03.md](../2025-12-03.md)（さまざまな keyframes のパターン：回転・拡大・ブラー解除など）  
- [JavaScript/2025-12-04.md](../2025-12-04.md)（Fashion サイトのローディング画面とヒーローアニメーション）  
- [JavaScript/2025-12-08.md](../2025-12-08.md)（ギャラリーのローディング演出とサムネイル hover アニメーション）  
- [JavaScript/2025-12-09.md](../2025-12-09.md)（メニュー・モーダル・バナーの開閉アニメーション）








