# JavaScript-ES6構文とデータ構造

## 概要
ES6（ES2015）で追加された主な構文と、配列・オブジェクトをモダンな書き方で扱うためのパターンをまとめる。  
`let / const`、アロー関数、テンプレートリテラル、分割代入・スプレッド構文、クラスの入口を整理する。[web:145][web:169]

---

## 1. let / const と var

```js
// 1. let / const と var
var x = 1;
var x = 2; // 上書きされる

let count = 0;
count = 1;  // OK
// let count = 2; // NG（同じスコープでの再宣言はエラー）

const PI = 3.14;
// PI = 3.15; // NG

const fruits = ['apple', 'fig'];
fruits.push('lemon');  // OK
```


- ルール：**まず const、必要なら let、var は使わない**。[file:3][web:145]

---

## 2. アロー関数（Arrow Functions）

### 2-1. 3種類の書き方

```js
// 2-1. アロー関数：3種類の書き方
function add(a, b) {
  return a + b;
}

const add2 = function (a, b) {
  return a + b;
};

const add3 = (a, b) => {
  return a + b;
};

const add4 = (a, b) => a + b;
```


- 関数宣言／関数式／アロー関数は「役割は同じ、書き方が違うだけ」。  
- コールバックや短い関数はアロー関数で統一すると読みやすい。[web:139][web:145]

### 2-2. 引数・戻り値のバリエーション

```js
// 2-2. アロー関数：引数・戻り値
const square = x => x * x;

const getRandom = () => Math.random();

const calcTax = (price, rate) => {
  const tax = price * rate;
  return price + tax;
};

const createUser = (name) => ({ name, active: true });
```


### 2-3. コールバックでの使いどころ

```js
// 2-3. アロー関数：コールバック
const fruits = ['apple', 'fig', 'lemon'];

fruits.forEach(function (fruit, index) {
  console.log(index, fruit);
});

fruits.forEach((fruit, index) => {
  console.log(index, fruit);
});

const btn = document.querySelector('#btn');
btn.addEventListener('click', () => {
  console.log('クリックされました');
});
```


- `forEach` / `map` / `filter` / `some` や `addEventListener` のコールバックはすべてアロー関数で書くつもりで OK。[file:2][file:5][web:139]

### 2-4. this の振る舞い（最小限）

```js
// 2-4. アロー関数と this
const counter = {
  count: 0,
  init() {
    window.addEventListener('click', () => {
      this.count++;
      console.log(this.count);
    });
  },
};
```


- オブジェクトのメソッドは通常のメソッド記法（`init() {}`）で書き、  
  内部のコールバックで「外側の this を使いたい」ときにアロー関数を使う。[web:133][web:139]

---

## 3. テンプレートリテラルとデフォルト引数


```js
// 3. テンプレートリテラルとデフォルト引数
const name = 'Taro';
const message = `こんにちは、${name} さん`;
console.log(message);

const html = `
  <div class="card">
    <h2>${name}</h2>
  </div>
`;

function greet(userName = 'ゲスト') {
  console.log(`ようこそ、${userName} さん`);
}

greet();
greet('Hanako');
```


- 文字列結合は基本的にテンプレートリテラルで行う。[web:145][web:146]

---

## 4. 分割代入とスプレッド構文

### 4-1. 分割代入（Destructuring）

```js
// 4-1. 分割代入
const fruits2 = ['apple', 'fig', 'lemon'];
const [first, second] = fruits2;
console.log(first);
console.log(second);

const product = { name: 'いちご', price: 450 };
const { name: productName, price } = product;
console.log(productName);
console.log(price);
```


- 配列やオブジェクトから「欲しいプロパティだけ取り出す」場面でよく使う。[file:5][web:145]

### 4-2. スプレッド構文（Spread）

```js
// 4-2. スプレッド構文
const arr1 = [1, 2, 3];
const arr2 = arr1;
const arr3 = [...arr1];

arr1.push(4);
console.log(arr2);
console.log(arr3);

const a = [1, 2];
const b = [3, 4];
const c = [...a, ...b];

const base = { name: 'いちご', price: 450 };
const withStock = { ...base, stock: 10 };
```


- 「参照を共有したくないとき」はスプレッド構文でコピーするのが定番パターン。[file:5][web:141]

---

## 5. ES6 クラスの入り口（class）

### 5-1. ES5 と ES6 の対応

```js
// 5-1. ES5 と ES6 のクラス
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function () {
  console.log('Hello, ' + this.name);
};
const alice1 = new Person('Alice');
alice1.greet();

class Person2 {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log('Hello, ' + this.name);
  }
}
const alice2 = new Person2('Alice');
alice2.greet();
```


- `class` は「コンストラクタ＋prototype」を分かりやすく書くための構文糖衣（中身はプロトタイプベースのまま）。[web:154][web:156]

### 5-2. かんたんな継承（イメージだけ）

```js
// 5-2. クラスの継承
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  speak() {
    console.log(this.name + ' barks.');
  }
}

const pochi = new Dog('Pochi', 'shiba');
pochi.speak();
```

- `extends` で「〜を元にしたクラス」を作り、`super()` で親クラスの処理を呼び出せる。[web:157][web:166]  
- 今の授業範囲では「設計図から同じ形のオブジェクトを何個も作れる」というイメージが持てれば十分。

---

## 関連デイリーノート

- [JavaScript/2025-11-27.md](../2025-11-27.md)（ES6 の導入、const/let、アロー関数の基本）  
- [JavaScript/2025-12-01.md](../2025-12-01.md)（配列メソッドと参照型の挙動、スプレッド構文）  
- [JavaScript/2025-12-02.md](../2025-12-02.md)（分割代入・オブジェクト配列＋テンプレートリテラル）  
- [topics/JavaScript-クラスとオブジェクト指向.md](./JavaScript-%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%A8%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91.md)（クラスとプロトタイプチェーンの詳細予定）








