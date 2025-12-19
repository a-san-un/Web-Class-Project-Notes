# JavaScript-開発環境とツール

## 概要
JavaScript の授業で使っている開発環境（VS Code＋Live Server＋Chrome DevTools）と、快適にコードを書くための設定・ツールをまとめたトピック。  
ローカルサーバーの起動方法、ブラウザでの動作確認、エラーの確認とデバッグの基本フローを整理する。

---

## 1. VS Code の基本構成

- エディターとして **Visual Studio Code** を使用。拡張機能で HTML/CSS/JavaScript の開発を効率化する。  
- プロジェクト構成の例：  
  - `Project/`  
    - `index.html`  
    - `css/style.css`  
    - `js/script.js`  

### よく使う拡張機能
- **Live Server**：ローカル開発サーバーを立ち上げて、保存時にブラウザを自動リロードしてくれる。[web:114][web:122]  
- （任意）ESLint / Prettier：コードのフォーマットや簡単な構文チェック用。  

参考：  
- [Live Server - VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)

---

## 2. Live Server でローカルサーバーを起動

### 起動手順
1. VS Code でプロジェクトフォルダを開く。  
2. `index.html` を開いた状態で、右下の **Go Live** ボタンをクリック。  
3. 既定のブラウザが起動し、`http://127.0.0.1:5500`（ポート番号は環境によって異なる）でページが表示される。  

### 右クリックから起動する方法

```
エクスプローラーで index.html を右クリック
→ 「Open with Live Server」を選択
```


### よくあるミス
- HTML ファイルではなく、単体の `.js` ファイルだけを開いていると Go Live ボタンが出ない。  
- 既に他のプロセスが同じポートを使っていて起動に失敗する場合があるので、そのときは Live Server の設定からポートを変更する。[web:113][web:119]

---

## 3. Chrome DevTools での確認

- Live Server で表示されたページを **Chrome** で開き、`F12` または `Cmd+Opt+I` で DevTools を起動する。  
- 主に使うパネル：  
  - **Elements**：HTML と CSS の確認・一時的な変更。[web:117][web:123]  
  - **Console**：エラーメッセージと `console.log` の確認。[web:106]  
  - **Sources**：ブレークポイントを貼ってデバッグ（必要に応じて）。[web:110]

### Console でのテスト

```js
// その場で JavaScript を試す
console.log('Hello DevTools');
document.querySelector('h1').textContent = 'テスト中';
```


---

## 4. jsconfig.json と IntelliSense（必要に応じて）

- プロジェクトが大きくなってきたとき、VS Code の IntelliSense（補完）を安定させるために `jsconfig.json` を用意することがある。  
- 小規模な授業プロジェクトでは必須ではないが、「補完が効かない」「別プロジェクトのコードが混ざる」といったときに検討する。

### シンプルな例

```json
{
  "compilerOptions": {
    "target": "es2020"
  },
  "exclude": ["node_modules", "**/dist/**"]
}
```


参考：  
- [Working with JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript)[web:121][web:124]

---

## 5. 開発の基本フロー

1. **VS Code でファイルを編集**  
   - HTML / CSS / JS を保存（`Ctrl+S` / `Cmd+S`）。  
2. **Live Server でブラウザを自動リロード**  
   - ファイル保存後、ブラウザ側で変更が反映されているか確認。  
3. **Chrome DevTools でエラーとログを確認**  
   - Console タブで赤いエラーが出ていないか、`console.log` で値が想定どおりかチェック。  
4. 必要に応じて **Sources パネルでブレークポイント** を貼り、`debugger` 文やステップ実行で原因を追う。[web:110][web:104]

---

## 6. よくあるトラブルと対処

- **ブラウザで JS が動かない**  
  - `file://` で開いていないか（必ず Live Server など `http://` で開く）。[web:116]  
  - `<script src="js/script.js"></script>` のパスが正しいか確認。  

- **更新しても画面が変わらない**  
  - ファイルを保存していない。  
  - Live Server を再起動する（ステータスバーの `Port: 5500` 表示などを確認）。  

- **コンソールにエラーが出ている**  
  - エラーの種類と行番号を確認し、`JavaScript-エラーとデバッグ.md` の手順に従って原因を特定する。  

---

## 関連ノート

- [JavaScript-エラーとデバッグ.md](./JavaScript-%E3%82%A8%E3%83%A9%E3%83%BC%E3%81%A8%E3%83%87%E3%83%90%E3%83%83%E3%82%B0.md)  
- [JavaScript-プロパティの調べ方.md](./JavaScript-%E3%83%97%E3%83%AD%E3%83%91%E3%83%86%E3%82%A3%E3%81%AE%E8%AA%BF%E3%81%B9%E6%96%B9.md)



