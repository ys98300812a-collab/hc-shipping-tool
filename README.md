# 📦 宏勤生活科技 - 出貨明細轉單系統 (Hong Qin Order Conversion System)

**當前版本 (Version)**: v3.3  
**系統屬性**: 前端單頁應用程式 (SPA) / 無伺服器架構 (Serverless)  
**主要技術棧**: HTML5, Vanilla JavaScript, SheetJS (xlsx), html2pdf.js, Sortable.js  

---

## 📑 系統概述 (System Overview)
本系統專為宏勤生活科技（Hong Qin Life Technology）出貨部門打造，旨在解決各大電商平台或內部系統匯出之非結構化 Excel/CSV 訂單報表，將其標準化並批次轉換為適合列印與歸檔的 PDF 出貨明細單。系統採用 **100% 客戶端運算 (Client-Side Processing)**，無需依賴後端伺服器，確保企業營運資料的高效處理與絕對安全。

---

## ✨ v3.3 核心架構與功能升級 (Key Features & Updates)

### 1. 智慧資料解析與清洗 (Intelligent Data Parsing)
* **日期序列化修復**：內建自動轉換器，精準攔截並解析 Excel 底層的 1900 紀元日期序號 (Epoch time, e.g., `46155`)，並自動格式化為標準的 `YYYY-MM-DD`，徹底解決匯出報表常見的日期亂碼問題。
* **動態欄位映射 (Dynamic Data Mapping)**：系統載入原始資料後，會自動透過正規表達式匹配核心欄位 (訂單編號、SKU、品名、數量等)。若來源報表格式異動，使用者亦可透過 GUI 下拉選單重新綁定資料節點。

### 2. 進階狀態管理與覆蓋機制 (State Management & Override Logic)
* **單據全域客製化 (Full-Document Mutation)**：突破唯讀限制，提供單筆訂單的深度編輯面板。支援修改「出貨日期」、「備註」以及「陣列層級的商品明細 (SKU / 品名 / 數量)」。
* **編輯鎖定保護 (Edit-Lock Protection)**：當使用者對特定訂單進行手動修改後，系統會將變更寫入記憶體的暫存字典 (`individualOverrides`) 中。再次點擊「產出預覽」時，系統會優先調用手動覆寫的快取資料，避免客製化內容被 Excel 原始配置覆蓋。

### 3. 動態客製化渲染 (Dynamic Component Rendering)
* **自訂延伸欄位 (Custom Extra Fields)**：支援無限擴充 PDF 表頭資訊。可指定讀取 Excel 中的特定欄位，或手動輸入常數值（如：促銷代碼、包裝指示）。
* **拖曳排序 (Drag-and-Drop Sortability)**：整合 Sortable.js，使用者可直覺拖曳 (⠿) 調整延伸欄位的渲染優先級與排列順序。

### 4. 企業級資安宣告 (Security & Privacy)
* **離線優先 (Offline-First)**：所有資料讀取 (FileReader API)、解析 (SheetJS) 與 PDF 渲染 (html2canvas + jsPDF) 皆於使用者本地瀏覽器的 Sandbox 內執行。
* **零資料外洩 (Zero Data Telemetry)**：訂單內容與客戶 PII (個人身分資訊) **絕對不會**透過任何 API 傳送至外部伺服器，完全符合企業資安稽核規範。

---

## 🛠️ 環境需求 (Prerequisites)
* **作業系統**: Windows 10/11, macOS, 或 Linux
* **瀏覽器支援**: 建議使用最新版的 **Google Chrome**, **Microsoft Edge**, 或 **Brave Browser** 以獲得最佳的 V8 引擎解析速度與 PDF 排版相容性。(不建議使用舊版 Safari 或 IE)
* **網路連線**: 僅初次開啟網頁載入外部 CDN 函式庫時需要網路。載入完成後，後續轉單作業可完全離線執行。

---

## 📖 標準操作流程 (SOP)

1. **資料載入 (Data Ingestion)**
   * 將匯出的 `.xlsx` 或 `.csv` 報表拖曳至系統指定的 Dropzone 區域。
2. **參數配置 (Configuration)**
   * 檢視並確認「核心欄位對應」是否準確映射。
   * 依需求新增、設定並拖曳排序「客製額外資訊」。
3. **編譯與預覽 (Compile & Preview)**
   * 點擊 `[1. 產出預覽]` 觸發虛擬 DOM 渲染。系統將依據訂單編號 (Order ID) 進行資料聚合 (Data Grouping) 並生成分頁預覽。
4. **單據微調 (Granular Editing) - *選用***
   * 針對需特殊處理的訂單，點擊左側 `[📝 修改此單內容]` 展開編輯面板。
   * 修改完畢後點擊 `[儲存修改]`，系統會將該訂單標記為「已鎖定 (Overridden)」。
5. **批次輸出 (Batch Export)**
   * 點擊 `[2. 下載所有 PDF]`，系統將觸發 html2pdf 引擎，將所有 DOM 節點轉換為高解析度 A4 尺寸的 PDF 檔案。

---

## ⚠️ 異常排除與例外處理 (Troubleshooting)

| 異常狀況 (Symptom) | 系統邏輯 (System Logic) | 解決方案 (Resolution) |
| :--- | :--- | :--- |
| **變更了 Excel 對應欄位，但預覽畫面沒更新** | 該訂單已被手動修改並寫入快取，觸發了「編輯鎖定保護」。 | **單筆處理**：點擊該訂單編輯面板內的 `[重設此單]`。<br>**批次處理**：點擊總控台的 `[清除所有手動修改]` 清空快取字典。 |
| **訂單時間顯示為五位數字** | 來源報表將日期儲存為 Excel Serial Date，而非字串。 | 確保已更新至 **v3.3** 版本，系統內建的 `formatExcelDate()` 函數將自動處理反序列化。 |
| **下載的 PDF 版面跑位或截斷** | 瀏覽器縮放比例或螢幕解析度干擾了 html2canvas 的擷取。 | 下載前請確保瀏覽器縮放比例為 **100%**，並避免在下載運算期間滾動視窗。 |

---
*Developed & Maintained for Hong Qin Life Technology Internal Operations.*
