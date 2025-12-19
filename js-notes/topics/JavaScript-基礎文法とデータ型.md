# JavaScript-基礎文法とデータ型

## 概要
JavaScript の「値の種類」と「基本的な書き方」を整理したトピック。  
プリミティブ型とオブジェクト型の違い、変数宣言、真偽値・論理演算子、Math を使った数値処理をまとめる。

---

## 1. データ型の種類

- プリミティブ型：string, number, boolean, undefined, null など単一の値を表す型。`bigint` や `symbol` なども含まれる。  
- オブジェクト型：配列・オブジェクトリテラル・関数など複数の値やプロパティをまとめて扱う型で、参照で渡される。

### ポイント
- プリミティブは「値そのもの」、オブジェクトは「箱（参照）」が渡されると考えると、代入や変更時の挙動の違いを理解しやすい。  
- 型の一覧や挙動の詳細は MDN を参照：  
  - [MDN: JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)  
  - [MDN: Primitive (プリミティブ)](https://developer.mozilla.org/ja/docs/Glossary/Primitive)

---

## 2. 変数宣言とスコープ

- `var` / `let` / `const` の3種類があり、現在は基本的に `let` と `const` を使う。`var` は関数スコープで巻き上げなどの癖がある。  
- 再代入が必要なときは `let`、定数として扱いたい値や参照を変えない配列・オブジェクトには `const` を使うのが基本。

### ポイント
- `const` で宣言した配列やオブジェクトも、中身の変更（`push` など）は可能で、参照自体を別の値に再代入できないだけ。  
- for 文のカウンタや、一時的な値だけ `let` にすることで、意図しない再代入バグを減らせる。

参考：  
- [MDN: JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)

---

## 3. 真偽値・Truthy / Falsy・論理演算子

- 真偽値（boolean）は `true` と `false` の2つで、条件分岐の結果は必ずどちらかに評価される。  
- JavaScript には `false, 0, "", null, undefined, NaN` など「falsy」とみなされる値があり、それ以外はすべて「truthy」として if 文で true と扱われる。

### ポイント
- `&&`（AND）は左から順に評価し、途中で falsy が出るとそこで止まりその値を返す。`||`（OR）は最初の truthy を返すので、デフォルト値設定に使える。  
- `!value` は「falsy なら true」、`!!value` は値を明示的に boolean に変換するテクニックとして利用できる。

参考：  
- [Explain Truthy & Falsy values in JavaScript](https://www.geeksforgeeks.org/javascript/explain-the-concept-of-truthy-falsy-values-in-javascript/)  
- [Truthy and falsy values in JavaScript](https://sentry.io/answers/truthy-and-falsy-values-in-javascript/)

---
## 4. 数値リテラルと四則演算

- 数値リテラルは `10`, `3.14`, `-5` など。JavaScript の number 型は整数・小数を区別せず同じ型で扱う。  
- 主な演算子は `+`（加算）、`-`（減算）、`*`（乗算）、`/`（除算）、`%`（余り）、`**`（累乗）など。

### 例
```js
let a = 10 + 3; // 13
let b = 10 - 3; // 7
let c = 10 * 3; // 30
let d = 10 / 3; // 3.333...
let e = 10%% 3; // 1
let f = 2 ** 3; // 8

let count = 0;
count++; // 1 （インクリメント）
count += 2; // 3 （複合代入）
```
### よくあるミス
- `"10" + 2` のように、片方が文字列だと数値計算ではなく**文字列連結**になる。  
  → 計算したいときは `Number("10") + 2` のように数値に変換してから足す。  
- 演算子の優先順位を忘れて、`10 + 2 * 3` を `36` と勘違いする。  
  → 実際は乗算が先に計算されて `16` になる。迷ったら `(10 + 2) * 3` のように括弧で明示する。

参考：  
- [jsprimer: データ型とリテラル](https://jsprimer.net/basic/data-type/)  
- [MDN: JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)

---
## 5. 数値と Math オブジェクト

- `Math.round` / `Math.floor` / `Math.ceil` で四捨五入・切り捨て・切り上げを行い、小数を扱う際に使う。  
- `Math.random()` は 0以上1未満の乱数を返し、`Math.floor(Math.random() * n)` で 0〜n-1 の整数を作れるため、配列からランダムに要素を選ぶ場面などで活躍する。

### ポイント
- ある範囲の乱数を作るときは「幅×Math.random()＋最小値」という形を意識するとミスが減る（例：1〜6は `Math.floor(Math.random() * 6) + 1`）。  
- 数値でない値が混ざると `NaN` になることがあるため、フォーム入力などでは `Number()` などで変換した上で計算する。

参考：  
- [MDN: Math.random()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Math/random)

---

## 6. 文字列・テンプレートリテラル（基礎）

- 文字列は `"..."` / `'...'` / `` `...` `` で書けるが、テンプレートリテラル `` `Hello ${name}` `` を使うと変数展開を含むメッセージを読みやすく書ける。  
- `.length` で文字数を取得し、`replace` で置き換え、`toUpperCase` で大文字化するなど、よく使うメソッドがある。

### ポイント
- 絵文字などサロゲートペアを含む文字列では `.length` が見た目の文字数と一致しないことがあり、その点に注意する。  
- `+` での連結よりテンプレートリテラルを使うと、改行や複数の変数を含む長いメッセージでもミスが減る。

参考：  
- [MDN: JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)

---

## 7. 値渡しと参照渡し

### 例

```js
// プリミティブ（値渡し）
let x = 10;
let y = x;
y = 20;
// x は 10 のまま

// オブジェクト（参照を共有）
let a = [1, 2];
let b = a;
b.push(3);
// a も [1, 2, 3] になる
```

### よくあるミス
- 「`const` で宣言した配列をコピーしたつもりが、元の配列まで書き変わっていた」  
  → 実際には同じ配列オブジェクトへの参照を共有しているため。`const b = [...a];` のようにスプレッド構文でコピーすると安全。  
- 関数の引数にオブジェクトを渡したとき、関数内で書き換えると元の変数も変わることを忘れがち。

参考：  
- [JavaScript 変数の 値型 と 参照型](https://zero-plus-one.jp/javascript/javascript-value-type/)  
- [JavaScript の値渡しと参照渡し](https://zenn.dev/ryu0947/articles/ff9117c214911e)

---

## 8. 型変換（暗黙／明示）

### 例

```js
// 暗黙の型変換
"10" + 2;      // "102" 文字列連結
"10" - 2;      // 8   数値として計算
if ("") {...}  // 空文字は falsy

// 明示的な型変換
Number("10");      // 10
String(10);        // "10"
Boolean("hello");  // true
```

### よくあるミス
- `"10" + 2` のように、つもりは足し算なのに文字列連結になってしまう。  
  → 計算したいときは先に `Number("10")` で数値に変換してから足す。  
- フォーム入力値をそのまま比較して、「`"2" > "10"` が true になる」など文字列比較になってしまう。  

参考：  
- [jsprimer: データ型とリテラル](https://jsprimer.net/basic/data-type/)  
- [MDN: JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)

---

## 9. リテラル表記と意味

### 主なリテラル
- 数値リテラル：`10`, `3.14` など。  
- 文字列リテラル：`"hello"`, `'world'`, `` `template` ``。  
- 真偽値リテラル：`true`, `false`。  
- `null`：意図的に「値がない」ことを表す。  
- `undefined`：変数を宣言しただけで値がまだ代入されていない状態などを表す。

### よくあるミス
- `null` と `undefined` をなんとなく同じものとして扱ってしまう。  
  → 「`null` は開発者が“ここは空です”と明示する値」「`undefined` は JavaScript 側が“まだ何もない”状態を表すことが多い」と意識して使い分ける。  

参考：  
- [jsprimer: データ型とリテラル](https://jsprimer.net/basic/data-type/)  
- [MDN: JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)

---

## 関連デイリーノート

- [JavaScript/2025-11-21.md](../2025-11-21.md)（JavaScript の概要・オブジェクト/メソッド/パラメータ、alert・console）  
- [JavaScript/2025-11-27.md](../2025-11-27.md)（if 文、ES6 の const/let、テンプレートリテラル）  
- [JavaScript/2025-11-28.md](../2025-11-28.md)（Truthy/Falsy、Boolean 型、文字列操作）  
- [JavaScript/2025-12-01.md](../2025-12-01.md)（配列・参照型と値型、論理演算子）  
- [JavaScript/2025-12-02.md](../2025-12-02.md)（for 文、Math オブジェクト、let/const の復習）
