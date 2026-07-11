# 甲種鍋爐操作人員 — 學術科整合備考系統 (行動 PWA 版)

這是為甲種鍋爐操作人員考試設計的備考系統，已升級為手機自適應版（RWD）並支援 PWA（Progressive Web App）離線安裝。

## 📱 手機 App 安裝說明
本系統支援離線使用。只要完成 GitHub Pages 的設定，就能在手機上將本系統「安裝」為獨立 App：

1. **啟用 GitHub Pages**：
   * 進入本 Repository 的 **Settings** -> **Pages**。
   * 將 **Build and deployment** 底下的 **Branch** 設定為 `main` 分支（資料夾保持為 `/ (root)`）。
   * 點選 **Save** 儲存。
   * 約 1 分鐘後，上方會出現您的專屬 HTTPS 網址。

2. **手機端下載與安裝**：
   * **iPhone (Safari)**：使用 Safari 開啟網址 `https://chuangchuang917-ux.github.io/boiler-trainer/`，點擊下方「分享」按鈕，選擇 **「加入主畫面」(Add to Home Screen)**。
   * **Android (Chrome)**：使用 Chrome 開啟網址 `https://chuangchuang917-ux.github.io/boiler-trainer/`，點選右上角選單，選擇 **「安裝應用程式」** 或 **「新增至主畫面」**。

3. **離線使用**：
   * 安裝完成後，手機桌面上會出現「甲鍋訓練系統」圖示。
   * 點開即為全螢幕 App 畫面，**在無網路環境（如捷運、考場外）依然可正常刷題與閱讀**，且答題進度與錯題本會儲存在手機中。

## 🛠️ 本專案核心檔案
* `index.html`：網頁主程式（單網頁應用，包含題庫與詳解數據）。
* `manifest.json`：設定 App 的啟動資訊與圖示。
* `sw.js`：Service Worker 腳本，處理離線快取控制。
* `boiler_app_icon.png`：App 桌面的火焰圖示。
