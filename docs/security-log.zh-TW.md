# 安全性紀錄

[English](security-log.md) | 繁體中文

- 日期: 2026-10-25
- 分支: `fix/security-upgrades`
- 範圍: `package.json`、`package-lock.json`

## 摘要

重新產生 lockfile，並為 react-scripts 固定的套件加上 npm overrides，npm audit 的高風險項目從 18 個降為 4 個。剩下的都在已停止維護的 Create React App 開發伺服器相關套件中。

## 修改內容

| 項目              | 修改前 | 修改後                                                                                         | 原因                                                                                          |
| ----------------- | ------ | ---------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| package-lock.json | -      | regenerated                                                                                    | 間接依賴改用已修補的版本（jsonpath、form-data、shell-quote、websocket-driver、proxy-addr 等） |
| npm overrides     | -      | nth-check, postcss, serialize-javascript, webpack-dev-middleware, node-forge, underscore, svgo | 強制使用已修補的版本，避免被 react-scripts 5.0.1 固定在舊版                                   |

## 尚未解決的問題

### 被上游限制的套件版本

| 套件                    | 版本   | 漏洞                | 無法修正的原因                                                      | 影響與因應                                                                                        |
| ----------------------- | ------ | ------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| webpack-dev-server      | 4.15.2 | 6 筆（5.2.x 修正）  | react-scripts 5.0.1 要求 webpack-dev-server 4，5 版與其設定不相容。 | 執行 `npm start` 期間，若瀏覽惡意網站，對方可能讀取原始碼或攔截 HMR WebSocket；正式建置不受影響。 |
| webpack-dev-middleware  | 5.3.4  | 1 筆（7.4.6 修正）  | webpack-dev-server 4 要求 5 版。                                    | 只影響開發伺服器的路徑穿越。                                                                      |
| node-forge              | 1.4.0  | 1 筆（尚無修正版）  | 目前沒有修正版。                                                    | RSA PKCS#1 v1.5 簽章驗證會接受多餘資料；只被 `selfsigned` 用來產生開發伺服器的 HTTPS 憑證。       |
| uuid                    | 8.3.2  | 1 筆（11.1.1 修正） | 由 webpack-dev-server 4 內的 sockjs 帶入。                          | 呼叫端自行傳入 buffer 給 v3/v5/v6 時缺少邊界檢查；sockjs 不會這樣用。只影響開發伺服器。           |
| postcss-selector-parser | 6.1.5  | 1 筆（7.1.6 修正）  | react-scripts 內附的 PostCSS 外掛要求此版本。                       | 建置時解析惡意 CSS 可能耗盡 CPU；只會解析你自己的 CSS。                                           |
| @tootallnate/once       | 1.1.2  | 1 筆（2.0.1 修正）  | 由 jest-environment-jsdom 27 的 jsdom 16 帶入。                     | 控制流程範圍錯誤；只在測試環境使用。                                                              |

### 需要修改程式才能解決

- 要清掉剩下的漏洞，必須把 Create React App（react-scripts）換成仍在維護的建置工具（例如 Vite），並同步修改 README 的範本步驟。

## 驗證方式

- `npm run build`、測試與 `npm start` 皆可正常執行。
- 以 osv-scanner 掃描 `package-lock.json`，只剩上表的套件。

## 未驗證項目

- 無其他項目：剩下的問題只影響本機開發伺服器與測試環境。
