# JavaScript-配列とオブジェクト

## 概要
複数の値をまとめて扱うための配列とオブジェクトについて整理したトピック。  
配列の基本操作・メソッド・for 文と forEach、オブジェクト配列と分割代入、参照型ならではの挙動をまとめる。

---

## 1. 配列の基本

- 配列リテラルは `[1, 2, 3]` や `['apple', 'banana']` のように書き、`array[0]` のようにインデックスでアクセスする。  
- `array.length` で要素数を取得し、存在しないインデックス（`array[10]` など）は `undefined` になる。

### 例

```js
const fruits = ['apple', 'fig', 'lemon'];
console.log(fruits[0]);      // 'apple'
console.log(fruits.length);  // 3
console.log(fruits[3]);      // undefined
```

### よくあるミス
- インデックスは 0 始まりということを忘れて「3 つ目＝index 3」と書いてしまう。  
- `length` を固定値だと思い込んでいて、`push` / `pop` などで増減するときの挙動を見落とす。

---

## 2. 配列の更新メソッド（破壊的）

- `push` / `pop`：末尾に追加、末尾から削除。  
- `shift` / `unshift`：先頭から削除、先頭に追加。  
- `reverse`：配列の順番を逆転させる。  

### 例

```js
const fruits = ['apple', 'fig', 'lemon'];
fruits.push('orange'); // ['apple','fig','lemon','orange']
const last = fruits.pop(); // last = 'orange'

fruits.unshift('grape'); // ['grape','apple','fig','lemon']
fruits.shift(); // 先頭を削除 // ['apple','fig','lemon']
fruits.reverse(); // 要素の順番を反転
```

### よくあるミス
- 「`const` で宣言した配列は変更できない」と思い込む。  
  → 参照（どの配列か）は変えられないが、中身の変更は可能。  
- `reverse` が破壊的メソッドであることを忘れ、元の順番を必要としているのに上書きしてしまう。

---

## 3. 配列の for 文と forEach / map / filter / some

### for 文

```js
const trains = ['JR', 'Keio', 'Odakyu'];

for (let i = 0; i < trains.length; i++) {
  console.log(i, trains[i]);
}

const withNumber = trains.map((train, index) => `${index + 1}. ${train}`);
const onlyJR = trains.filter((train) => train.includes('JR'));
const hasKeio = trains.some((train) => train.includes('Keio')); // true
```

- インデックスを直接扱いたいときや、途中で `break` / `continue` したいときは従来の for 文が便利。

### forEach / map / filter / some

```js
const trains = ['JR', 'Keio', 'Odakyu'];
    trains.forEach((train, index) => {
    console.log(index + 1, train);
});

const withNumber = trains.map((train, index) => ${index + 1}. ${train});
const onlyJR = trains.filter((train) => train.includes('JR'));
const hasKeio = trains.some((train) => train.includes('Keio')); // true
```

- `forEach`：すべての要素に対して処理を行う（戻り値は使わない）。  
- `map`：各要素から新しい配列を作る。  
- `filter`：条件を満たす要素だけを集めた新しい配列を返す。  
- `some`：1つでも条件を満たす要素があれば true。

### よくあるミス
- `forEach` の中で `return` してもループ自体は止まらないことを忘れている。  
- `map` / `filter` で「`return` を書き忘れて `undefined` だらけ」になりがち。

---

## 4. insertAdjacentHTML と配列からの HTML 生成

- 画像ファイル名や商品データを配列で持ち、ループしながら `insertAdjacentHTML` でカードやリストを生成するパターンを使った。
### 例

```js
const lists = ['apple.jpg', 'fig.jpg', 'lemon.jpg'];
const menu = document.querySelector('#menu');

for (let i = 0; i < lists.length; i++) {
  const content = `
    <div>
      <img src="images/${lists[i]}" alt="">
    </div>`;
  menu.insertAdjacentHTML('beforeend', content);
}
```

### よくあるミス
- `insertAdjacentHTML` の位置指定（`'beforeend'` など）を間違えて、意図しない位置に挿入される。  
- ループ内で毎回 `querySelector` してしまい、無駄な DOM 検索が増える（外で一度だけ取得してから使うほうが良い）。

---

## 5. オブジェクトとオブジェクト配列

### 基本のオブジェクト

```js
const strawberry = {
    name: 'いちご',
    img: 'strawberry.jpg',
    price: 450,
    };

console.log(strawberry.name); // 'いちご'
```

- `{キー: 値}` の形でプロパティを持つ。  
- ドット記法 `obj.key` やブラケット記法 `obj['key']` でアクセスできる。

### オブジェクト配列とカード生成

```js
const lists = [
  { name: 'いちご', img: 'strawberry.jpg', price: 450 },
  { name: 'ライム', img: 'lime.jpg', price: 400 },
];

const menu = document.querySelector('#menu');

for (let i = 0; i < lists.length; i++) {
  const { name, img, price } = lists[i];
  const content = `
    <div>
      <img src="images/${img}" alt="">
      <h2>${name}</h2>
      <p>${price}円</p>
    </div>`;
  menu.insertAdjacentHTML('beforeend', content);
}
```

- 配列の各要素が「1つのカードに必要な情報」をまとめたオブジェクトになっていると、HTML への展開がシンプルになる。  

---

## 6. 分割代入とスプレッド構文（配列・オブジェクト）

### 分割代入

```js
const fruits = ['apple', 'fig', 'lemon'];
const [first, second] = fruits; // first='apple', second='fig'

const product = { name: 'いちご', price: 450 };
const { name, price } = product;
```

- 配列・オブジェクトから必要なプロパティだけを取り出すときに便利。  

### スプレッド構文（コピー）

```js
const arr1 = [1, 2, 3];
const arr2 = arr1;       // 参照を共有
const arr3 = [...arr1];  // 値をコピー

arr1.push(4);
console.log(arr2); // [1, 2, 3, 4]
console.log(arr3); // [1, 2, 3]
```

- 参照共有による「巻き添え変更」を避けたいときは `...` でコピーする。  

---

## 7. 参照型としての挙動（おさらい）

- 配列・オブジェクトはどちらも参照型で、代入すると「同じものを指す別名」が増えるイメージ。  
- 特にオブジェクト配列では、1つの要素を関数に渡して書き換えると、元の配列内のデータも更新される。

### 例

```js
const product = { name: 'いちご', price: 450 };
const p2 = product;
p2.price = 500;
// product.price も 500 になる
```

### よくあるミス
- 「一時変数に入れて編集したつもりが、元データも書き変わっていた」  
  → 本当にコピーしたいのか、同じものを共有したいのかを意識して選ぶ（コピーならスプレッド構文などを使う）。

---

## 関連デイリーノート

- [JavaScript/2025-12-01.md](../2025-12-01.md)（配列の基本・メソッド、Stack/Heap の解説）  
- [JavaScript/2025-12-02.md](../2025-12-02.md)（for 文と配列ループ、オブジェクト配列と分割代入）  
- [JavaScript/2025-12-04.md](../2025-12-04.md)（forEach / map / filter / some の使い分け）  
- [JavaScript/2025-12-10.md](../2025-12-10.md)（AJAX で取得した JSON 配列を forEach で HTML に変換）
