# iRent 智能車況管家 — 前端原型

2026 和泰 AI 黑客松：用 AI 優化 iRent 共享汽車的租還車體驗與營運效率。

本 repo 只放 **App 端前端原型與規格**。模型訓練、資料集、實驗腳本不在此（見 `.gitignore`）。

## 跑起來

```bash
python3 -m http.server 8000
open http://localhost:8000/取車拍照.html
```

必須用 server，不能直接 `file://` 開——`getUserMedia` 與 iOS 陀螺儀權限都要求 https 或 localhost。

## 檔案

| 檔案 | 說明 |
|---|---|
| [取車拍照.html](取車拍照.html) | 取車流程原型（P2–P8 / C1–C5），單檔 HTML，含手機模擬器 |
| [借還車流程.md](借還車流程.md) | **前端實作規格**：逐頁列出 component 與行為，取車 + 還車 |
| [asset/](asset/) | 示範照片與素材，`README.md` 列出還缺哪些圖 |
| [CONTEXT.md](CONTEXT.md) | 專案術語表 |
| [docs/adr/](docs/adr/) | 架構決策紀錄 |

## 原型現況

**已完成**：取車流程。四條路徑都走得通——回報沒問題／回報有問題後繼續租車／回報有問題後放棄租車／任一路徑遇到照片缺項。

**待做**：還車流程（規格已完備，見 `借還車流程.md` 第三節）、後台 dashboard（環節三，另開文件）。

**真的在運算的**：手機姿勢硬擋（陀螺儀）、模糊偵測（Laplacian 變異數）、低光偵測（平均亮度）。
**還是 mock 的**：角度覆蓋、車牌完整性——等模型。接縫收在 `fetchJudgement()` 一支函式，回傳格式就是未來的 API 契約，接真模型時只換函式內容。

## 設計決策

| ADR | 決策 |
|---|---|
| [0001](docs/adr/0001-plate-and-angle-completeness-soft-prompt.md) | 角度與車牌檢查採兩階段軟提示 + 被動記錄 |
| [0002](docs/adr/0002-lock-camera-to-landscape.md) | 拍照介面鎖橫式 |
| [0003](docs/adr/0003-hard-block-only-at-shutter.md) | 硬擋只保留在快門層，照片內容問題一律軟提示 |
| [0004](docs/adr/0004-cleanliness-realtime-damage-async.md) | 內裝清潔度即時攔截，新車損比對背景執行 |
