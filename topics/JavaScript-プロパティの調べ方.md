# JavaScript - プロパティの調べ方

**作成日**: 2025年12月1日
**カテゴリ**: JavaScript
**関連授業**: [2025-12-01.md](../JavaScript/2025-12-01.md) - Chapter 4-14, 4-15

***

## 概要

JavaScriptのプロパティやメソッドを調べる方法は、独学やトラブルシューティングで必須のスキルです。講師推奨の**2本立てアプローチ**を使うことで、効率的に正しい情報を見つけられます。

***

## 基本の2本立てアプローチ

### 1. MDN公式ドキュメントで当たりをつける

**MDN Web Docs**はMozillaが提供する信頼性の高いリファレンスです。

**検索方法**:

```
Google検索: "プロパティ名 MDN"
例: "scrollY MDN"
例: "scrollHeight MDN"
```

**MDNで確認すべきポイント**:

- プロパティの説明と用途
- 構文(Syntax)
- 返り値の型
- ブラウザ互換性(Browser compatibility)
- 使用例(Examples)

**MDN主要ページ**:

- [Window - MDN](https://developer.mozilla.org/ja/docs/Web/API/Window)
- [Document - MDN](https://developer.mozilla.org/ja/docs/Web/API/Document)
- [Element - MDN](https://developer.mozilla.org/ja/docs/Web/API/Element)
- [Array - MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array)

***

### 2. ブラウザDevToolsで実際に触って確認

ドキュメントで当たりをつけたら、実際にブラウザで動作確認します。

**DevToolsの開き方**:

- Windows/Linux: `F12` または `Ctrl + Shift + I`
- Mac: `Cmd + Option + I`

***

## DevToolsでの調査テクニック

### console.dir() でオブジェクトの中身を見る

`console.log()`よりも詳細な情報が見られます。

```javascript
// windowオブジェクトのすべてのプロパティを表示
console.dir(window);

// documentElementのすべてのプロパティを表示
console.dir(document.documentElement);

// 特定の要素を調査
const element = document.querySelector('#menu');
console.dir(element);
```

**使い方**:

1. Consoleタブで上記コードを実行
2. 表示された▶マークをクリックして展開
3. プロパティ一覧から目的のものを探す

***

### monitorEvents() でイベントを監視する

イベントがいつ発生しているか、どんな情報が含まれているかを確認できます。

```javascript
// windowのすべてのイベントを監視
monitorEvents(window);

// 特定のイベントだけ監視
monitorEvents(window, 'scroll');
monitorEvents(window, ['scroll', 'resize']);

// 監視を停止
unmonitorEvents(window);
```

**実行結果例**:

```
scroll Event {isTrusted: true, ...}
scroll Event {isTrusted: true, ...}
```


***

### イベントオブジェクトの全内容を表示

イベントリスナーに`console.log`を直接渡すと、イベントオブジェクトの詳細が見られます。

```javascript
// イベントオブジェクトをそのまま表示
window.addEventListener('scroll', console.log);

// より詳細な情報
window.addEventListener('scroll', (event) => {
    console.dir(event);
});
```


***

### Elements タブでリアルタイム確認

**手順**:

1. DevToolsの**Elements**タブを開く
2. 調査したい要素を選択
3. 下部の**Properties**タブをクリック
4. スクロールや操作をしながらプロパティの変化を観察

**確認できること**:

- `scrollY`の値がリアルタイムで変化
- `scrollHeight`と`clientHeight`の違い
- どのプロパティが使えるか

***

## 実践例: スクロール関連プロパティの調査

### Step 1: MDNで調べる

```
Google検索: "scroll JavaScript MDN"
```

**見つかる主要プロパティ**:

- `window.scrollY`: 縦方向のスクロール量
- `window.scrollX`: 横方向のスクロール量
- `element.scrollHeight`: 要素の全コンテンツ高さ
- `element.clientHeight`: 要素の表示領域の高さ

***

### Step 2: DevToolsで確認

```javascript
// Consoleで実行
console.log('現在のスクロール量:', window.scrollY);
console.log('ページ全体の高さ:', document.documentElement.scrollHeight);
console.log('表示領域の高さ:', document.documentElement.clientHeight);

// リアルタイム監視
window.addEventListener('scroll', () => {
    console.log(`scrollY: ${window.scrollY}`);
});
```


***

### Step 3: 実際に動かして理解

```javascript
// スクロール進捗率を計算
const getScrollPercent = () => {
    const scrolled = window.scrollY;
    const pageHeight = document.documentElement.scrollHeight;
    const viewHeight = document.documentElement.clientHeight;
    const percentage = Math.round(scrolled / (pageHeight - viewHeight) * 100);
    
    console.log(`進捗率: ${percentage}%`);
}

window.addEventListener('scroll', getScrollPercent);
```


***

## よく使うプロパティ一覧

### Window オブジェクト

| プロパティ | 説明 | 例 |
| :-- | :-- | :-- |
| `window.scrollY` | 縦方向のスクロール量 | `window.scrollY` |
| `window.scrollX` | 横方向のスクロール量 | `window.scrollX` |
| `window.innerHeight` | ウィンドウの内側の高さ | `window.innerHeight` |
| `window.innerWidth` | ウィンドウの内側の幅 | `window.innerWidth` |

### Document.documentElement

| プロパティ | 説明 | 例 |
| :-- | :-- | :-- |
| `scrollHeight` | コンテンツ全体の高さ | `document.documentElement.scrollHeight` |
| `scrollWidth` | コンテンツ全体の幅 | `document.documentElement.scrollWidth` |
| `clientHeight` | 表示領域の高さ | `document.documentElement.clientHeight` |
| `clientWidth` | 表示領域の幅 | `document.documentElement.clientWidth` |


***

## DevToolsの便利コマンド集

```javascript
// オブジェクトの中身を詳細表示
console.dir(object);

// イベント監視開始
monitorEvents(element);
monitorEvents(element, 'eventType');

// イベント監視停止
unmonitorEvents(element);

// 要素を取得(Elements タブで選択中の要素)
$0

// 複数要素を取得
$$('selector')  // querySelectorAll のショートカット

// 最後の実行結果を再利用
$_
```


***

## トラブルシューティング

### プロパティが undefined になる

**原因**:

- タイミングの問題(DOMの読み込み前)
- 要素が存在しない
- タイプミス

**確認方法**:

```javascript
const element = document.querySelector('#menu');
console.log(element);  // null なら要素が見つかっていない
console.dir(element);  // undefined でないか確認
```


***

### イベントが発火しない

**確認方法**:

```javascript
// イベントが登録されているか確認
monitorEvents(window, 'scroll');

// イベントリスナーを確認(Elements タブ > Event Listeners)
```


***

## 参考リンク

- [MDN Web Docs - JavaScript](https://developer.mozilla.org/ja/docs/Web/JavaScript)
- [Chrome DevTools Documentation](https://developer.chrome.com/docs/devtools/)
- [Console Utilities API](https://developer.chrome.com/docs/devtools/console/utilities/)

***

## 関連ノート

- [2025-12-01.md](../JavaScript/2025-12-01.md) - Chapter 4-14, 4-15でこのテクニックを使用
- [JavaScript-配列操作.md](./JavaScript-%E9%85%8D%E5%88%97%E6%93%8D%E4%BD%9C.md) - 配列メソッドの調べ方

***

## まとめ

**プロパティの調べ方 2ステップ**:

1. **MDNで当たりをつける** → 正確な情報と使い方を理解
2. **DevToolsで実際に触る** → 動作を確認して理解を深める

この方法を使えば、公式ドキュメントにない細かい挙動も自分で確認できます。困ったときは必ずこの2ステップで調査しましょう!

