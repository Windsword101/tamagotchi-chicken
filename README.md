# 小雞農場 — HTML + JavaScript 學習指南

## 專案結構

```
tamagotchi-learn/
├── index.html        ← 主檔案（HTML 結構 + CSS 樣式）
├── js/
│   ├── GameData.js   ← 模組 1：資料層（存檔/讀檔）
│   ├── UI.js         ← 模組 2：畫面層（更新 DOM）
│   └── Game.js       ← 模組 3：邏輯層（遊戲機制）
├── assets/           ← 27 張圖片素材
└── LEARN.md          ← 本學習指南
```

---

## 核心概念對照表

| Cocos Creator 概念 | HTML + JS 對應做法 |
|---|---|
| Scene（場景） | `index.html` 的 HTML 結構 |
| Node（節點） | `<div>`、`<img>`、`<button>` 等 HTML 元素 |
| Component（組件） | JavaScript 函式和物件 |
| Inspector 屬性綁定 | `document.getElementById('id')` |
| `@property` | JS 變數 |
| `tween` 動畫 | CSS `transition` + `animation` |
| `setInterval` 計時器 | 同名，直接用 |
| `localStorage` 存檔 | 同名，直接用 |
| Build 輸出 | 不需要！直接是可執行的檔案 |

---

## 學習路線

### 第 1 步：了解 HTML 結構

打開 `index.html`，找到以下幾個關鍵 HTML 元素：

```html
<!-- 每個元素都有 id，JS 用 id 來找到它 -->
<span id="hud-coins">💰 500</span>

<!-- onclick 是最簡單的事件綁定 -->
<button onclick="buyItem(0)">購買</button>

<!-- style.width 由 JS 控制，實現動態進度條 -->
<div class="bar-fill" id="bar-hunger" style="width: 80%"></div>
```

### 第 2 步：了解 CSS 動畫

在 `index.html` 的 `<style>` 區塊中，找到：

```css
/* @keyframes 定義動畫的每個階段 */
@keyframes float {
  0%, 100% { transform: translateY(0px); }
  50%       { transform: translateY(-8px); }
}

/* animation 屬性套用動畫 */
#chicken-img {
  animation: float 2.5s ease-in-out infinite;
}
```

### 第 3 步：了解資料存檔（GameData.js）

```javascript
// 存檔：把 JS 物件轉成 JSON 字串存入瀏覽器
function saveData(gameData) {
    localStorage.setItem('tamagotchi_v1', JSON.stringify(gameData));
}

// 讀檔：從瀏覽器取出 JSON 字串，轉回 JS 物件
function loadData() {
    const raw = localStorage.getItem('tamagotchi_v1');
    return raw ? JSON.parse(raw) : { ...DEFAULT_DATA };
}
```

### 第 4 步：了解畫面更新（UI.js）

```javascript
// 每次資料改變，呼叫這個函式更新畫面
function refreshUI(gameData) {
    // 修改文字
    document.getElementById('hud-coins').textContent = '💰 ' + gameData.coins;
    // 修改進度條寬度
    document.getElementById('bar-hunger').style.width = gameData.hunger + '%';
    // 修改圖片
    document.getElementById('chicken-img').src = 'assets/chick_happy.png';
}
```

### 第 5 步：了解遊戲邏輯（Game.js）

```javascript
// setInterval：每隔指定毫秒執行一次函式
// 8000 毫秒 = 8 秒
setInterval(decayStats, 8000);

// 購買道具的邏輯
function buyItem(index) {
    if (gameData.coins < item.cost) {
        showToast('金幣不足！');  // 顯示提示
        return;                   // 提前結束函式
    }
    gameData.coins -= item.cost;  // 扣除金幣
    saveData(gameData);           // 存檔
    refreshUI(gameData);          // 更新畫面
}
```

---

## 如何在本機測試

### 方法 A：VS Code Live Server（推薦）
1. 安裝 VS Code
2. 安裝擴充套件 **Live Server**
3. 右鍵點擊 `index.html` → **Open with Live Server**
4. 瀏覽器自動開啟，修改程式碼後自動重新整理

### 方法 B：直接用瀏覽器開啟
- 直接雙擊 `index.html` 用 Chrome 開啟即可
- 注意：部分瀏覽器對本機檔案有限制，建議用 Live Server

---

## 部署到 GitHub Pages（永久網址）

### 步驟 1：建立 GitHub Repository
1. 前往 [github.com](https://github.com) 登入
2. 右上角 **「+」→「New repository」**
3. 名稱填 `tamagotchi-chicken`，選 **Public**，點 **Create**

### 步驟 2：上傳所有檔案
1. 在新 repo 頁面點 **「uploading an existing file」**
2. 把整個 `tamagotchi-learn/` 資料夾**裡面的內容**全部拖曳上傳
   - `index.html` 必須在根目錄
   - `js/` 資料夾
   - `assets/` 資料夾
3. 點 **「Commit changes」**

### 步驟 3：開啟 GitHub Pages
1. 進入 repo → **Settings → Pages**
2. Source 選 **「Deploy from a branch」**
3. Branch 選 **「main」**，資料夾選 **「/ (root)」**
4. 點 **Save**

### 步驟 4：取得永久網址
等 1~2 分鐘後，網址為：
```
https://你的帳號.github.io/tamagotchi-chicken/
```

---

## 常見修改練習

### 練習 1：修改初始金幣
在 `js/GameData.js` 找到：
```javascript
const DEFAULT_DATA = {
    coins: 500,  // 改成你想要的數字
```

### 練習 2：修改狀態衰減速度
在 `js/Game.js` 找到：
```javascript
function decayStats() {
    gameData.hunger = clamp(gameData.hunger - 1);   // 改成 -2 衰減更快
    gameData.happy  = clamp(gameData.happy  - 0.5); // 改成 -1
```

### 練習 3：新增一個道具
在 `js/Game.js` 的 `SHOP_ITEMS` 陣列新增：
```javascript
{ name: '能量飲料', cost: 70, stat: 'energy', amount: 35 },
```
然後在 `index.html` 的商店面板新增對應的 HTML。

### 練習 4：修改角色對話台詞
在 `js/Game.js` 找到 `DIALOGS` 陣列，加入你想要的台詞：
```javascript
const DIALOGS = [
    '咕咕咕！', '我餓了...', '好開心！',
    '你好帥！',  // 新增台詞
```
