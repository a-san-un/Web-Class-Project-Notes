# JavaScript-エラーとデバッグ

## 概要
JavaScript 実行時に発生するエラーの読み方と、Chrome DevTools のコンソールを使ったデバッグ方法をまとめたトピック。  
よく出るエラーのパターン、エラーメッセージのどこを見るか、そしてエラーを見つけて直すための基本的な手順を整理する。

---

## 1. よく出るエラーの種類

- **SyntaxError**：構文エラー。カンマやカッコの閉じ忘れ、クォートの抜けなど、コードが「そもそも読めない」状態のときに出る。[web:92][web:99]  
- **ReferenceError**：存在しない変数・関数名を参照したときに出る（例：スペルミス、スコープ外の変数）。[web:94][web:108]  
- **TypeError**：データ型に合わない操作をしたとき（関数ではないものを関数として呼ぶ、`null` にプロパティアクセスなど）。[web:95][web:108]

### ポイント
- `ReferenceError: xxx is not defined` → 「xxx という変数・関数が定義されていない」  
- `SyntaxError: Unexpected token` → その行付近のカンマ・カッコ・クォートを重点的に確認する。  
- `TypeError: xxx is not a function` → 呼び出している対象が本当に関数か、代入で上書きしていないか確認する。  

参考：  
- [MDN: JavaScript エラーリファレンス](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Errors)  
- [MDN: Error / ReferenceError / SyntaxError](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Error)

---

## 2. エラーメッセージの読み方

- コンソールに表示されるメッセージは、基本的に「エラーの種類：説明 at ファイル名:行番号:列番号」という形式になっている。  
- まず「どのファイルの何行目か」を見て、その行付近のコードとメッセージ内容を照らし合わせる。

### 例

```js
Uncaught ReferenceError: outputHtml is not defined
    at script.js:42:3
```

- 「`outputHtml` という名前の関数が見つからない」「`script.js` の 42 行目付近を確認」という情報が読み取れる。  
- 関数名のスペルミス、定義より前で呼び出していないか、別ファイルで定義しているのに読み込んでいない、などを疑う。  

参考：  
- [MDN: JavaScript エラーリファレンス](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Errors)  
- [初心者向けのエラー読み方解説記事](https://programing-knowledge.net/javascript-error-reading-solution/)

---

## 3. Chrome DevTools コンソールの基本

- ブラウザの開発者ツール（DevTools）の「Console」タブでは、エラーメッセージの一覧と `console.log` の結果を確認できる。  
- `F12` で DevTools を開き、タブから「Console」を選択するか、`Cmd+Opt+J` / `Ctrl+Shift+J` のショートカットで直接開く。 [web:103][web:106]

### よく使うメソッド

```js
// コンソールでよく使うメソッド
console.log(value);     // 値を確認する基本
console.dir(obj);       // オブジェクトの中身をツリー表示
console.warn('注意');   // 警告表示（黄色）
console.error('エラー'); // エラー表示（赤）
```


- 「どこからおかしくなっているか」を知るために、処理の途中途中で値をログ出力して、想定どおりか確認する。  

参考：  
- [Chrome DevTools Console overview](https://developer.chrome.com/docs/devtools/console)  
- [Console パネルの入門記事](https://qiita.com/aqril_1132/items/a0f7e81a772006847ec3)

---

## 4. デバッグの基本手順

1. **エラーメッセージを読む**  
   - まず種類（Syntax / Reference / Type）とファイル名・行番号を確認する。  
2. **該当行の「少し前」からコードを見る**  
   - 構文エラーは 1 行前のカンマ・カッコが原因なことが多い。  
3. **`console.log` で値や処理の流れを確かめる**  
   - `if` の条件や、関数に渡されている引数などを出力して、期待している値か確認する。  
4. **サンプルコードとの diff を取る**  
   - 授業のサンプルと自分のコードを比較して差分を探すことで、打ち間違いや抜け漏れを見つけやすくなる。  

### 例：デバッグ用ログ

```js
// デバッグ用ログの例
function outputHtml(data) {
  console.log('outputHtml に渡ってきた data:', data);

  if (!Array.isArray(data)) {
    console.error('配列ではありません');
    return;
  }

  // ここに本来の処理を書く
}
```


---

## 5. debugger 文とブレークポイント（軽く）

- `debugger;` 文をコード中に書いておくと、DevTools を開いているときにその行で実行が一時停止し、変数の中身をステップ実行しながら確認できる。[web:110][web:104]  

```js
// debugger 文の例
function calcTotal(price, tax) {
  const total = price * (1 + tax);
  debugger; // ここで一時停止して変数の中身を確認できる
  return total;
}
```


- DevTools の「Sources」タブで行番号をクリックしてブレークポイントを貼ることもできる。  

---

## 6. エラーと仲良くなるための心構え

- エラーメッセージは「動いていないことを教えてくれるヒント」なので、怖がらずに**まず読む**。  
- いきなり全部を直そうとせず、「今 1 つ目のエラーだけに集中して直す → 再読み込み」を繰り返すと、落ち着いて対処できる。  
- 自分で 10 分調べても分からないときは、エラー文をコピーしてメモに貼るか、AI に「このエラーはどういう意味？」と聞くとよい。

---

## 関連デイリーノート

- [JavaScript/2025-12-10.md](../2025-12-10.md)（エラーの種類とチェックリスト、XMLHttpRequest/Fetch のエラー対応）  
- [JavaScript/2025-12-01.md](../2025-12-01.md)（DevTools コンソールの使い方メモ）  
- [JavaScript/2025-11-21.md](../2025-11-21.md)（`alert` / `console.log` / `console.dir` の基本）


