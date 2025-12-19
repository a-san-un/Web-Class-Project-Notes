# JavaScript-非同期通信とJSON

## 概要
ブラウザからサーバにデータを取りに行く「非同期通信」と、受け取った JSON データを JavaScript の配列・オブジェクトとして扱う方法をまとめたトピック。  
XMLHttpRequest と Fetch API + async/await の2つの書き方、および JSON の変換と HTML 出力のパターンを整理する。

---

## 1. JSON とは何か

- JSON（JavaScript Object Notation）は、オブジェクトや配列をテキストとして表現するためのフォーマットで、`{}` と `[]` を使ったシンプルな構造が特徴。  
- 通信のときは「文字列」として送受信されるため、JavaScript で扱うには `JSON.parse`（文字列 → オブジェクト）と `JSON.stringify`（オブジェクト → 文字列）を使う。

### 例

```js
const jsonText = '{"name":"car","price":200}';
const data = JSON.parse(jsonText);   // JS オブジェクトに変換
console.log(data.name);              // 'car'

const str = JSON.stringify(data);    // ふたたび JSON 文字列へ
```

---

## 2. XMLHttpRequest で JSON を取得する

### 基本の流れ
1. `new XMLHttpRequest()` でインスタンスを作る。  
2. `open('GET', url)` でリクエスト内容を設定する。  
3. `onload` / `onerror` にコールバックを設定する。  
4. `send()` で送信する。

### 例：ボタンを押すごとに JSON を読み込む

```js
const btn = document.querySelector('#btn');
const info = document.querySelector('#car-info');
const request = new XMLHttpRequest();  // 1つだけ生成

let counter = 1; // cars1.json → cars2.json → cars3.json

btn.addEventListener('click', () => {
  request.open(
    'GET',
    `https://norixx.github.io/jquery_elearing/cars${counter}.json`
  );

  request.onload = () => {
    if (request.status === 200) {
      const data = JSON.parse(request.responseText); // JSON文字列 → 配列
      outputHtml(data);
    } else {
      alert('サーバへの接続はできましたが、データが取得できませんでした');
    }
  };

  request.onerror = () => {
    alert('接続エラー');
  };

  request.send();

  counter++;
  if (counter > 3) {
    btn.setAttribute('disabled', 'disabled');
  }
});
```


---

## 3. JSON 配列を HTML に変換するパターン

### 例：車の一覧を HTML にして追記

```js
function outputHtml(data) {
  let htmlStr = '';

  data.forEach((car) => {
    const colorStr = car.colors.join(', ');
    htmlStr += `
      <p>
        ${car.name}は${car.brand}の${car.type}タイプの車です。<br>
        色の展開: ${colorStr}
      </p>`;
  });

  info.insertAdjacentHTML('beforeend', htmlStr);
}
```


### よくあるミス
- `let htmlStr = ''` を forEach の中に書いてしまい、毎回リセットされて 1 件分しか表示されない。  
- `join()` を忘れて `car.colors` をそのまま連結しようとして `[object Object]` などが表示される。

---

## 4. Fetch API + async/await

- Fetch API は `fetch(url)` で `Promise<Response>` を返すモダンな API。  
- async 関数の中で `await fetch(...)` と `await res.json()` を使うと、XMLHttpRequest + `JSON.parse` と同じ処理をよりシンプルに書ける。

### 例：1 回だけ cars1.json を取得

```js
async function fetchCars() {
  try {
    const result = await fetch(
      'https://norixx.github.io/jquery_elearing/cars1.json'
    );
    console.log(result);

    if (result.status === 200) {
      const carsData = await result.json(); // JSON → JS配列
      console.log(carsData);
    } else {
      alert('サーバ接続はできましたが、ファイルの取得ができませんでした。');
    }
  } catch (error) {
    console.log(error);
  }

  console.log('エラーが発生しています');
}

fetchCars();
```


### イベントと組み合わせる場合

```js
btn.addEventListener('click', async () => {
  try {
    const res = await fetch(
      `https://norixx.github.io/jquery_elearing/cars${counter}.json`
    );
    if (!res.ok) {
      alert('取得に失敗しました');
      return;
    }
    const data = await res.json();
    outputHtml(data);
  } catch (error) {
    console.log(error);
  }
});
```


---

## 5. sessionStorage / localStorage と JSON

- Web Storage API（`sessionStorage` / `localStorage`）には文字列しか保存できないため、オブジェクトは `JSON.stringify` してから保存する。  
- 表示済みバナーの状態などを保存して、一定時間は再表示しない、といった用途に使う。

### 例：バナー表示状態を保存

```js
function setBannerData() {
  return JSON.stringify({
    show: true,
    expire: Date.now() + 10000, // 10秒後に期限切れ
  });
}

const isBannerShow = sessionStorage.getItem('bannershow');
const parsed = isBannerShow ? JSON.parse(isBannerShow) : null;

if (!parsed) {
  showBanner();
  sessionStorage.setItem('bannershow', setBannerData());
} else if (Date.now() > parsed.expire) {
  sessionStorage.removeItem('bannershow');
  showBanner();
  sessionStorage.setItem('bannershow', setBannerData());
}
```


---

## 6. XMLHttpRequest と Fetch の使い分けメモ

- **XMLHttpRequest**  
  - 古いブラウザやレガシーコードでよく使われる。  
  - `onload` / `onerror` などイベントベースで処理を書く。  

- **Fetch API**  
  - 現在の標準的な書き方。Promise ベースで、async/await と相性が良い。  
  - `res.ok` や `res.status` のチェックを自分で書く必要がある。

---

## 関連デイリーノート

- [JavaScript/2025-12-10.md](../2025-12-10.md)（XMLHttpRequest による JSON 取得と HTML 出力、Fetch + async/await のサンプル）  
- [JavaScript/2025-12-09.md](../2025-12-09.md)（sessionStorage を使ったバナー表示制御）



