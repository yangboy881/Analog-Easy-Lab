# img/mascot — 深海主題角色立繪

「鯨魚女僕 · 深海」風格左上角那隻角色，優先用**立繪圖**；找不到圖時會自動退回內建的
`#sharkMaid` SVG（所以就算圖不見了，頁面也不會壞）。

目前掛**三張**，網頁每次開站隨機挑一張（同一頁不會換，重整才會換）：

| 檔名 | 來源作品 | 原始檔 |
|---|---|---|
| `whale_maid_1.png` / `whale_maid_face_1.png` | 《你看又急》DeepSeek娘表情包 | `你看又急.png`（1254×1254） |
| `whale_maid_2.png` / `whale_maid_face_2.png` | 《鯨打細算：抱算盤的鯨魚女僕》 | `鯨打細算：抱算盤的鯨魚女僕.png`（1280×1280） |
| `whale_maid_3.png` / `whale_maid_face_3.png` | 《DeepSeek娘 Q 版鯨魚女僕立繪》 | `DeepSeek娘 Q 版鯨魚女僕立繪.jpg`（500×710） |
| `whale_maid.png` / `whale_maid_face.png` | 舊的單張版本（保留當後備） | — |

## 怎麼換圖（不用自己轉檔）

1. 把立繪存成 **`img/mascot/whale_maid_src.png`**（PNG / JPG / WebP 都可以，白底沒關係）；
   或者直接把圖丟進專案資料夾 / Downloads，什麼檔名都行。
2. 在專案根目錄執行：

   ```
   py mascot_prep.py                       # 讀 img/mascot/whale_maid_src.png → whale_maid.png
   py mascot_prep.py --auto                # 自動挑資料夾裡最新的那張圖
   py mascot_prep.py 你看又急.png --suffix 1   # → whale_maid_1.png + whale_maid_face_1.png
   ```

   它會自動：去掉白底（只挖跟邊界連通的背景，角色的白圍裙、白頭飾不會被挖穿）→ 裁掉多餘留白
   → 等比縮到高 360px → 輸出 `whale_maid.png`（首頁橫幅用）＋ `whale_maid_face.png`
   （128×128 頭像，新手專區右上角 54px 小圖用）。加了 `--suffix N` 就輸出 `whale_maid_N.png`。

3. 要換掉三張輪替圖：跑三次 `--suffix 1 / 2 / 3` 覆蓋掉同名檔即可，
   或改 `OTA Tuner_v5.html` 裡的 `MASCOT_ART` 陣列（src / face / label）。
4. 重新整理 `OTA Tuner_v5.html`（Ctrl+Shift+R），切到「鯨魚女僕 · 深海」。

想改顯示大小或換來源檔：

```
py mascot_prep.py 另一張圖.jpg --height 480
py mascot_prep.py 深色背景圖.png --thresh 12    # 背景不是純白時把容差調小
```

## 檔案

| 檔名 | 用途 |
|---|---|
| `whale_maid_src.png` | 原始立繪（沒有也可以，`--auto` 會去別的地方找） |
| `whale_maid_N.png` | 去背＋縮圖後的全身圖，首頁橫幅用（`N` = 1..3 隨機） |
| `whale_maid_face_N.png` | 由全身圖上半部裁出的 128×128 頭像，新手專區標題列用 |

圖片抓不到時，頁面會自動退回內建的 `#sharkMaid` SVG，不會壞版。

### 版面機制（改 CSS 前先看這段）

首頁橫幅與新手專區右上角都是「**固定方形框 + 圖絕對置中**」：

```html
<div class="shark-art">
  <img data-mascot="hero" onerror="this.remove()">   <!-- 立繪，絕對置中、不撐版面 -->
  <svg><use href="#sharkMaid"/></svg>                 <!-- 內建線稿，只當尺寸保底 -->
</div>
```

* 框是 `118×118px`（手機 88×88；右上角小圖 54×54 / 手機 42×42），所以三張不同長寬的圖
  都會被居中、文字左緣也不會跟著圖寬跳動。
* CSS 用 `.shark-art > img ~ svg { visibility: hidden; }`：**有圖就把線稿藏起來**。
  這一行不能刪 —— 圖是疊在線稿上面的，PNG 的透明處會透出後面的向量鯨魚，看起來就是「疊圖／雙影」。
* 圖片 404 時 `onerror` 會把 `<img>` 整個移除，上面的選擇器就不再成立，內建線稿自動回來。


## 版權提醒（重要）

* 自己畫／自己用 AI 生成的圖：可以直接放進來。
* **現在這三張不是自製圖**，來源是「藍色大肥魚」（非官方同人整理站，`藍色大肥魚.com`）的
  DeepSeek 娘相簿，站方在每一張作品頁寫的是：
  * 授權：**暫未標註**
  * 授權說明：**「圖片版權歸原作者所有；原作者未標註。」**
  * 來源：QQ 群投稿／未標註，來源作者：**原作者未標註**

  也就是說：**沒有取得授權、也找不到可以署名的人**。目前只在頁面上標「非官方同人二創 ·
  版權歸原作者所有」，屬於「低調使用 + 願意下架」的做法；若要公開發佈、營利、放廣告或做
  商品，務必先換成自己畫／自己生成的圖（跑 `mascot_prep.py --suffix N` 覆蓋即可），
  或去該站「署名、來源或下架申請」頁面處理授權。
  * 原始頁面：
    * https://xn--pssy23gqgbz2d718b.com/zh-hant/works/deepseek202609300025.html （你看又急）
    * https://xn--pssy23gqgbz2d718b.com/zh-hant/works/deepseek202610210007.html （Q 版立繪）
    * https://xn--pssy23gqgbz2d718b.com/zh-hant/works/deepseek202611200004.html （鯨打細算）
* 「DeepSeek 娘」本身是 DeepSeek 的吉祥物，角色形象另有商標／品牌規範；
  本專案與 DeepSeek 官方無關，頁面也標成「非官方同人二創」。

