# 官方來源與版本辨識

查核日期：2026-11-22。已驗證本機版本：`hc 0.1.21`（`herdr-core`）、`herdr-app 0.1.16`。以下是來源索引與摘要，不是完整官方手冊；網路 master 會變動，實際操作優先核對已安裝版本的 help／skill。

> 歸屬聲明：Herdr（`herdr-core`、`herdr-app`）為 [herdrdev](https://github.com/herdrdev) 所有；本專案為獨立開源的第三方 Agent Skill，非官方專案。

| 官方來源 | 用途 |
| --- | --- |
| [Herdr 官網](https://herdr.dev/) | app、core、行動端與 Herdr TUI 的產品入口 |
| [herdr-app README](https://github.com/herdrdev/herdr-app/blob/master/README.md) | 桌面版安裝、更新、平台與 agent 入口 |
| [App 命令表](https://github.com/herdrdev/herdr-app/blob/master/docs/commands.md) | `herdr-app do` 的正式命令與參數 |
| [App layout 與 mod 指南](https://github.com/herdrdev/herdr-app/blob/master/defaults/AGENTS.md) | app home 指南的上游來源 |
| [Mod 文件入口](https://github.com/herdrdev/herdr-app/blob/master/docs/mods.md) | 自訂入口、check 與生成型別文件位置 |
| [共享 labels](https://github.com/herdrdev/herdr-app/blob/master/docs/labels.md) | pinned、seen 與跨客戶端 turn 語意 |
| [App 安全與隱私](https://github.com/herdrdev/herdr-app/blob/master/docs/security.md) | trusted mods、資料與隱私邊界 |
| [herdr-core README](https://github.com/herdrdev/herdr-core/blob/master/README.md) | 持續終端與 core 架構入口 |
| [Core 命令表](https://github.com/herdrdev/herdr-core/blob/master/docs/commands.md) | 從 binary help 產生的 `hc` CLI 參數 |
| [Core machines](https://github.com/herdrdev/herdr-core/blob/master/docs/machines.md) | SSH 與其他主機連線 |
| [Core templates](https://github.com/herdrdev/herdr-core/blob/master/docs/templates.md) | 代理角色與啟動模板 |

本次直接查核 app README、app commands／mods／labels、core commands，以及本機 `herdr-core --help`、`herdr-core --skill`、`herdr-app --help`、`herdr-app --skill`；其餘連結為按需深入閱讀入口。

## 三套介面不能混用

- **herdr-core／hc**：持續終端、程式執行與 agent 協作；與舊 `herdr` multiplexer 不同。
- **herdr-app**：本技能處理的桌面 GUI；spaces、tabs、panes、JavaScript mods。
- **Sheprd／舊 Herdr UI**：另有 `sheprd browser` 與 Luau mods 等介面。[Sheprd README](https://github.com/sheprd-labs/sheprd/blob/main/README.md) 可用來辨識既有安裝，不能據此推定 herdr-app 有同名功能。

搜尋結果可能混合歷史命名與不同專案。不要僅以安裝資料夾名稱或 `HERDR_ENV=1` 判定正在使用哪套 API；先查可執行檔及其 help。本次查核時，`herdrdev/herdr-app` 與 `herdrdev/herdr-core` 的預設分支均為 `master`，不是 `main`。

## 安裝與更新

使用者要求安裝時再依 app README 選平台方式，先檢視官方安裝腳本；不在研究或診斷過程執行下載後立即執行的安裝命令。安裝方式、支援平台與更新機制可能變動，屆時重查。

app 與 core 可有各自版本。某個命令缺失時先查該 binary 的 help，不猜替代參數，不為取得功能自行升級。舊版 `herdr` 或 Sheprd 設定不自動遷移到新版 app。
