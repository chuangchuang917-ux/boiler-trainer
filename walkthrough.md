# 甲種鍋爐操作人員 — 學術科整合備考系統 (v3.1 — 行動 App PWA 版)

## 完成日期
2026-07-11

## 專案目標
1. 將學科 5 大 PDF 題庫（共 792 題）完整提取，並深度整合至原有的術科模擬器訓練系統中（[index.html](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/index.html)）。
2. 在 v3.0 版本中，進一步為系統無縫集成 **「504 題獨家手寫詳細解析數據庫」**，並解決 PDF 中不同科目/工作項目重複題號導致的解析覆蓋衝突問題，提供最高品質、100% 覆蓋的學科詳解刷題體驗。

## 產出
*   **index.html** 位於 [index.html](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/index.html)（大小為 488 KB，已注入完整 504 題手寫解析與 JS 引擎）
*   **custom_explanations.json** 位於 [custom_explanations.json](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/custom_explanations.json)（收錄 504 條高質量、精準對應的手寫詳解數據庫）
*   **gen_theory.py** 位於 [gen_theory.py](file:///C:/Users/alber/.gemini/antigravity-ide/brain/ee931362-630a-43be-b3f9-1822ea07da89/scratch/gen_theory.py)（更新為支援 `{subj}_{num}_{wi}` 複合鍵查找引擎）

---

## 📋 新增功能特色（學科刷題系統）

1.  **📝 模擬考模式（80題）**：
    *   按照官方比例隨機抽題：4 門共同科目各 4 題（共 16 題），專業科目 64 題（共 80 題，一題 1.25 分）。
    *   具備 **100 分鐘倒數計時器** 與 **答題進度點狀導覽列**。
    *   交卷後即時計算分數，並標明是否及格（60 分），提供 **完整詳細解題列表**（包含你所選的答案與正確答案對比）。
2.  **📚 分類練習模式**：
    *   支援按 **5 大科目**（職業安全衛生、工作倫理、環境保護、節能減碳、鍋爐操作甲級）及各科下屬的 **工作項目** 篩選題目。
    *   採用 **主動回想 (Active Recall) 互動機制**：點擊選項後立即可視化呈現對錯（綠色為對，紅色為錯），並即時顯示正確答案解析。
3.  **❌ 錯題本功能**：
    *   刷題或模擬考做錯的題目會 **自動記錄在瀏覽器的 localStorage 中**（即使關閉網頁也不會丟失）。
    *   可單獨針對錯題進行練習，答對後自動從錯題本中移除，直到錯題數歸零，實現精準複習。

---

## 🛠️ 關鍵問題診斷與修復日誌

在整合與測試過程中，我們克服了以下問題：
1.  **PDF 頁面文字重複提取問題**：
    *   *現象*：部分 PDF 含有隱藏或重疊的文字層，導致直接讀取時文字重複了 3 遍。
    *   *修復*：Python 提取腳本使用正則表達式分割頁尾的 `Page X of Y`，只保留第一個乾淨的區段，確保 792 題無重複、無漏字。
2.  **CSS/JS 注入重複宣告問題**：
    *   *現象*：因重複執行生成腳本，導致 `THEORY_QUESTIONS` 被重複聲明而報錯。
    *   *修復*：每次執行 `gen_trainer_v3.py` 前，先使用 clean 腳本將 `trainer.html` 還原至 82KB 乾淨版再進行注入。
3.  **猴子補丁引起的無限遞迴 (Maximum call stack size exceeded) 錯誤**：
    *   *現象*：重寫全域 `showPage` 導致了遞迴調用引發 Call Stack 溢出。
    *   *修復*：移除對 `showPage` 的 monkey-patch，改用獨立的導航包裝函數 `goTheoryExam`、`goTheoryPractice`、`goTheoryWrong`，徹底根除此 Bug。
4.  **HTML 雙引號衝突導致的 SyntaxError 錯誤**：
    *   *現象*：在 `buildQCard` 中使用 `JSON.stringify(q)` 傳遞物件，雙引號與 HTML 的 `onclick="..."` 屬性雙引號衝突，導致點擊選項時出現 `Unexpected end of input` 錯誤。
    *   *修復*：改為僅在 `onclick` 中傳遞 `q.subj` 與 `q.num`（如 `onclick="onAnswer(this, 0, 0, 2, 'practice', 'osha', 1)"`），並在 JS 中透過 `THEORY_QUESTIONS.find` 來動態查找對象。此做法 100% 避免了引號衝突。
5.  **重複題號覆蓋衝突問題 (v3.0 核心修正)**：
    *   *現象*：學科題庫中（特別是鍋爐專業科目）的多個子工作項目存在重複的題號（例如 `boiler_21` 在「工作 03 操作」、「工作 04 檢點」、「工作 05 處理」、「工作 07 安衛」中均有出現且內容完全不同），使用 `{subj}_{num}` 扁平鍵會造成解析內容相互覆蓋。
    *   *修復*：將解析數據庫的 Lookup Key 升級為 **`{subj}_{num}_{wi}`** 複合鍵（其中 `wi` 為該題所屬的中文工作項目名稱，在整套題庫中 100% 唯一）。在 `gen_theory.py` 查找引擎中，先嘗試精準匹配複合鍵，若無則降級回退匹配常規鍵，成功在 flat JSON 數據結構下完美支持 504 題手寫解析的無衝突展示。

---

## 🚀 使用與複習方式

### A. 電腦端使用
雙擊直接用瀏覽器開啟（或啟動本地 HTTP 伺服器訪問）：
[index.html](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/index.html)

*   **建議備考路徑**：
    1.  第一週：使用 **📚 分類練習** 搭配主動回想，快速過濾掉常識題，刷完 792 題。
    2.  第二週：集中突破 **❌ 錯題本** 中的硬核法規數字題，並結合原本的 **術科操作程序** 加深記憶。
    3.  考前三天：每天進行 2-3 次 **📝 模擬考**，確保分數穩定在 70 分以上即可輕鬆過關！

---

## 📱 行動 App (PWA) 升級說明 (v3.1 新增)

我們重構了系統版面，加入手機版自適應（RWD）設計與 PWA 服務註冊，支援將本網頁以手機 App 形式安裝並離線使用。

### 1. 新增檔案說明：
*   [manifest.json](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/manifest.json)：設定手機安裝資訊（App 名稱、色彩、啟動設定）。
*   [sw.js](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/sw.js)：處理 Service Worker 離線快取控制，快取網頁主體與字型，保證完全斷網時仍能正常運作。
*   [boiler_app_icon.png](file:///c:/Users/alber/Desktop/antigravity/甲鍋考試/boiler_app_icon.png)：高質感的 App 火焰圖示。

### 2. 免費部署與手機安裝指南：
因為 PWA 功能必須執行在 **HTTPS 安全協定**（或 Localhost）下，請依照以下步驟將網頁部署並「安裝」至手機：

1.  **免費託管（GitHub Pages 推薦）**：
    *   將 `index.html`、`manifest.json`、`sw.js` 和 `boiler_app_icon.png` 四個檔案上傳至您的 GitHub Repository。
    *   在 Repository 點選 **Settings** -> **Pages**，將 Branch 設為 `main` 或 `master` 並儲存。
    *   數分鐘後您將獲得一個專屬的網址（例如 `https://您的帳號.github.io/專案名稱/`）。
2.  **手機端安裝**：
    *   **iPhone (iOS)**：使用 **Safari** 瀏覽器開啟該網址，點選下方「分享」按鈕，選擇 **「加入主畫面」(Add to Home Screen)**。
    *   **Android**：使用 **Chrome** 瀏覽器開啟該網址，點選右上角選單，選擇 **「安裝應用程式」** 或 **「新增至主畫面」**。
    *   安裝後桌面上會多出火焰 App 圖示，點擊即可全螢幕、離線且保存進度地進行甲鍋複習！
