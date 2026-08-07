# asset/

前端原型（`取車拍照.html`，之後還有還車端）用到的素材。

**檔名一律用 ASCII、小寫、連字號。** 中文加空白的檔名在 GitHub、CI、跨 OS 工具鏈上都要 URL encode，是無謂的地雷。原始資料夾（`沒有進行索賠/`、`進行索賠資料/`）保持原樣不動，這裡放的是副本。

---

## 已有：車外四角示範照

來源：官方提供的「沒有進行索賠」資料集，車號 RCG-2235。全部 **1280×720 橫式**——這也順帶驗證了 ADR 0002 鎖橫式的決定，官方資料本來就是橫的。

| 檔名 | 原始檔名 |
|---|---|
| `rcg2235-front-left-pickup.jpg` | `沒有進行索賠/RCG2235左前 借.jpg` |
| `rcg2235-front-right-pickup.jpg` | `沒有進行索賠/RCG2235右前 借.jpg` |
| `rcg2235-rear-left-pickup.jpg` | `沒有進行索賠/RCG2235左後 借.jpg` |
| `rcg2235-rear-right-pickup.jpg` | `沒有進行索賠/RCG2235右後 借.jpg` |
| `rcg2235-front-left-return.jpg` | `沒有進行索賠/RCG2235左前 還.jpg` |
| `rcg2235-front-right-return.jpg` | `沒有進行索賠/RCG2235右前 還.jpg` |
| `rcg2235-rear-left-return.jpg` | `沒有進行索賠/RCG2235左後 還.jpg` |
| `rcg2235-rear-right-return.jpg` | `沒有進行索賠/RCG2235右後 還.jpg` |

**demo 情境怎麼用這幾張**（對應 `取車拍照.html` 的 `SCENARIOS`）：

| 情境 | 用哪四張 | 為什麼 |
|---|---|---|
| 缺左後角度 | front-left-pickup / front-right-pickup / **rear-right-pickup** / rear-right-return | 第 3 格該拍左後卻又是右後，畫面上看得見判定說的「方向拍成右後」 |
| 僅無完整車牌 | 四張 `-return` | 其中 `rcg2235-front-right-return.jpg` 經 `plate_annotation_result.csv` 人工標註為看不到完整車牌 |
| 全部通過 | 四張 `-pickup` | 角度齊、車牌清楚 |

> 注意：「僅無完整車牌」那組只有 front-right-return 經人工確認沒有完整車牌，另外三張未標註過。判定本來就是 mock，不影響操作，但講稿避開這點比較保險。

---

## 待補：四項

### ① `car-hero.png` — 車輛示意圖
- **用在**：P2「檢查車輛四周」、R4「檢查車輛狀況」中央
- **內容**：白色 Toyota Prius C（資料集那台）3/4 前側視角，去背
- **規格**：PNG 透明底，720×420，@2x
- **參考**：`取車流程圖.png` 第 3 個畫面正中央
- **接法**：把 `取車拍照.html` 裡的 `.placeholder`（標「素材待補 ①」）換成 `<img src="asset/car-hero.png">`

### ② `illust-reported.png` — 回報後說明插圖
- **用在**：P4「了解，這台車有狀況」中央
- **內容**：示意「已回報／客服會處理」，沿用 iRent 既有插畫風格
- **規格**：PNG 透明底，600×340
- **可省略**：刪掉佔位框即可，不影響流程

### ③ `damage-1.jpg` ～ `damage-4.jpg` — 車損特寫（最重要）
- **用在**：P5 拍照序列的車損段取景畫面
- **內容**：真實車損近拍（刮痕／凹痕／保桿破損），看得出損傷位置
- **規格**：**橫式 16:9**，1280×720 以上
- **接法**：填進 `取車拍照.html` 的 `DAMAGE_PHOTOS` 陣列
  ```js
  const DAMAGE_PHOTOS = ["damage-1.jpg","damage-2.jpg","damage-3.jpg","damage-4.jpg"];
  ```
- **現況**：空陣列 → 按快門產生標示「素材待補 ③」的灰底佔位圖，流程照跑
- **踩雷提醒**：`進行索賠資料/` 裡那 8 張「車況回報」照都是 **3024×4032 直式**，直接用會被 16:9 裁掉大半

### ④ `slot-front-left.png` 等 ×4 — 方位示意圖
- **用在**：相機底部四格未拍時的引導圖
- **內容**：灰色車輛線稿，左前／右前／左後／右後各一
- **規格**：PNG 透明底，128×88，線條 `#9AA0A8`
- **現況**：顯示文字「左前／右前／左後／右後」，功能完整只是不夠像
- **參考**：`還車流程圖.png` 相機畫面底部那排小灰車圖示

### ⑤ 車內與加油卡示範照 ×3 — 還車專用
- **用在**：`還車拍照.html` 拍照序列的前三格（加油卡／停車卡 → 車內前座 → 車內後座）
- **需要**：
  - `interior-front.jpg` — 車內前座，**髒汙**狀態（要能演 R8 攔截）
  - `interior-rear.jpg` — 車內後座
  - `fuel-card.jpg` — 駕駛座上方遮陽板的加油卡／停車卡套
- **規格**：橫式 16:9，1280×720 以上
- **哪裡找**：`車外車內照片上傳清單.csv` 記了 **前車內 4700 張、後車內 4644 張**，附 Azure blob 下載網址，只是沒被下載到本機。先測一筆 URL 還活著沒：
  ```bash
  curl -sI "$(python3 -c "import csv;r=csv.DictReader(open('車外車內照片上傳清單.csv',encoding='utf-8-sig'));print(next(x['下載網址'] for x in r if x['上傳分類(常有誤)']=='前車內'))")" | head -1
  ```
  回 200 就整批抓；回 403 再自己拍
- **接法**：填進 `還車拍照.html` 的 `SLOT_DEFS`，把對應那格的 `photo:null` 換成檔名
- **現況**：按快門產生標示「素材待補 ⑤」的灰底佔位圖，流程照跑

---

## 上傳到 GitHub 前

這些照片是**和泰／iRent 提供的解題資料**，不是我們的。公開 repo 等於對外散布，且一旦被索引／快取，刪掉也未必收得回來。照片裡有實際車輛與車牌。

建議：先確認主辦方對解題資料的使用範圍，或直接用 private repo。若要公開，可以把 `asset/` 加進 `.gitignore`，只推程式碼與規格文件。
