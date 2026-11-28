# img/svg — schemdraw 產出的電路圖

這個資料夾的 `.svg` 全部由專案根目錄的 Python 腳本產生，**不要手改**（下次重跑就被蓋掉）。

| 檔案 | 產生它的腳本 | 用在哪 |
|---|---|---|
| `bgr_core.svg` / `.theme.svg` | `bgr_core.py` | `OTA Tuner_v4.html` → 進階專區 → BGR 第 1 頁「架構與優缺點」第一張（原理版核心）；<b>進階練習 02「BGR 溫度曲線」</b>頁尾 |
| `bgr_impl.svg` / `.theme.svg` | `bgr.py` | 同上頁「實作版長什麼樣子」（vertical PNP / grounded-collector） |
| `ldo_full.svg` / `.theme.svg` | `ldo.py` | 進階專區 → LDO 第 1 頁「完整電路」；<b>進階練習 01「LDO 負載暫態」</b>頁尾 |
| `cp_dickson.svg` / `.theme.svg` | `cp_dickson.py` | 進階專區 → Charge Pump (`chp`) 第 2 頁「變體、效率與常見錯誤」開頭；<b>進階練習 05「Charge Pump 升壓」</b>頁尾（Dickson，2 級 + 省略號） |
| `pll_loop.svg` / `.theme.svg` | `pll_loop.py` | 進階練習 04「PLL 除頻鎖定」頁尾（PFD + CP + 迴路濾波器 + VCO + ÷N） |
| `sar_adc.svg` / `.theme.svg` | `sar_adc.py` | 進階練習 03「SAR 逐次逼近」頁尾（S/H + CMP + CDAC + SAR 邏輯） |
| `buck.svg` / `.theme.svg` | `buck.py` | 獨立教材（目前沒放進頁面） |

* 目前頁面是 **`OTA Tuner_v5.html`**（`figs_build.py` 會優先挑 v5，沒有才退回 v4；
  也可以用環境變數 `OTA_HTML` 指定檔名）。
* 同一個圖名可以出現在頁面多個地方（例：`ldo_full` 同時放在知識頁與練習頁），
  `figs_build.py` 會把**所有**同名區塊都換掉。
* 練習頁（`adlLdo` / `adlBgr` / `adlSar` / `adlPll` / `adlCp`）的圖是放在 `.lab2`
  兩欄面板**下方**的整寬 `<figure>`；不要塞進 `.panel` 裡——那欄只有 ~270 px，
  字會縮到看不見。

* 沒有 `.theme` 的是原檔（黑線、透明底），適合貼進講義 / 列印。
* `.theme.svg` 把 `black` 換成 `currentColor`、字體換成頁面的 mono 字體、筆畫 2.0 → 1.5，
  並在根節點掛 `color:var(--ink)`，所以 inline 進深色頁面時會跟著主題變色。
* **字色要注意**：schemdraw 的線條用 CSS（`stroke:black`），文字用的是屬性
  （`fill="black"`）。`figlib.theme_markup()` 兩種都要換，否則切到深色主題時
  線是亮的、字卻還是黑的（2026-11 修過這個 bug）。改完用
  `py _tmp_debug\_figcheck.py` 確認每個 FIG 區塊 `attrBlack=0`。

## 建置流程

```powershell
py bgr_core.py     # 或 bgr.py / ldo.py / buck.py / cp_dickson.py / pll_loop.py / sar_adc.py：重畫並輸出到 img/svg
py figs_build.py   # 把 *.theme.svg 同步進 OTA Tuner_v5.html 的 <!-- FIG:name --> 標記
py figs_build.py --check   # 只檢查頁面裡的圖有沒有過期
```

頁面裡的標記長這樣（HTML 註解，執行時會變成 inline SVG）：

```html
<figure class="fig">
<!-- FIG:bgr_impl --><!-- /FIG:bgr_impl -->
<figcaption>…</figcaption>
</figure>
```

要新增一張圖：寫一支 `xxx.py` → `figlib.save(d, 'xxx')` → 在頁面放一對
`<!-- FIG:xxx -->` 標記 → 跑 `py figs_build.py`。顯示寬度上限在 `figs_build.py`
的 `ARIA` 字典裡調（`max-width`）。
