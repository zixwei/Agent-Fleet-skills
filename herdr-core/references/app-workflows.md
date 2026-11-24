# App 工作流程

## 檢查與明確定址

```text
herdr-app --version
herdr-app ls
herdr-app status
herdr-app commands
```

`ls/status` 連到已開啟的視窗；`commands` 不需要視窗。連不上時回報連線問題，不為查詢啟動或重啟 app。`HERDR_APP_HOME` 可選擇 app home，預設為 `~/.config/herdr-app`；不要自行改它來操控另一個使用者視窗。

terminal ID 與 pane ID 是不同命名空間；從 ls 找對應關係，不能把 `t7` 當成 `p7`。命令參數以當前 `commands` 為準，未知參數會失敗。

## 使用者要求顯示工作時

```text
herdr-app do space.new --name api --folder /path/to/api
herdr-app do space.new --name local-api --folder /path/to/api --machine ""
herdr-app do tab.new --name "login review" --space SPACE_ID
herdr-app do tab.new --space SPACE_ID --mod my-panel --props '{"repo":"api"}'
herdr-app do pane.split --direction right --pane PANE_ID
herdr-app do agent.focus --terminal t7
herdr-app do terminal.open --folder /path/to/api --place tab
herdr-app do terminal.keep --terminal t7 --place tab
```

只在使用者要求時建立 space／tab；給明確名稱。新建預設會顯示，適用命令可用 `focus: false` 保留焦點，先確認目前版本支援。launch 回覆成功僅表示終端建立；仍需用 core 查程式是否成功啟動。

- **終端操作**：`terminal.open`（`--folder`／`--place`）開啟新終端；`terminal.keep` 把背景終端顯示到 GUI（與 core `hc keep` 的遺失恢復功能不同）；`terminal.park` 會移除其 pane 並停用 core keep，程式繼續運行，不要把它當成無副作用的單純隱藏；`terminal.pin` 釘選、`terminal.peek`／`terminal.show` 檢視、`terminal.kill` 結束終端且會一併結束其子代理；另有 `terminal.select*`、`terminal.scroll`、`terminal.search` 供畫面互動查詢。
- **Tab／Pane／Overlay 操作**：`tab.settle`／`tab.unsettle` 調整分頁固定狀態；`pane.swap`、`pane.zoom`、`pane.focus`、`pane.go` 控制分割窗格；`tab.new` 與 `pane.split` 可帶 `--mod` 與 `--props` 載入 mod 視圖；`overlay.close` 關閉覆蓋層；`app.reset`／`layout.reset` 具破壞性重置效果，未經明確要求不執行。
- **遠端與本機 Space**：遠端 space 用 `space.new` 的 `machine` 與遠端 `folder`；明確指定本機 space 可傳 `--machine ""`。腳本未指定 machine 時使用本機，與 GUI 按鍵／點擊繼承當前 space machine 的行為不同。
- **`do` 呼叫契約**：Agent 呼叫 `herdr-app do` 絕不會開啟缺參數對話框，必須傳齊必要參數。成功時回傳建立的 ID JSON；失敗時輸出 `{"error": "..."}` 並以 exit 1 結束。若啟動的指令在 2 秒內非零退出，GUI 會顯示錯誤 toast。`do` 可接受 `--key value`（陣列／物件傳 JSON）或單一完整 JSON 物件；目標 ID 必須是字串，`null` 的省略語意可能回到目前焦點。PowerShell 多欄位 JSON 用 `ConvertTo-Json -Compress`，複雜流程寫成 `.ps1`，不要把 shell escaping 當 JSON escaping。

## Core 與畫面的橋接

core 終端的 `herdr.space=<space-id>` label 讓它加入 app；`herdr.tab=<tab-id>` 指定 tab，未指定可能建立新 tab。一般背景 worker 不加這些 labels。

`space.close`、`tab.close`、`pane.close` 會結束所含終端；`window.close` 是另一種操作。操作前確認需求與目標，不能用猜測的 ID 或 `confirmed` 跳過授權。絕不在使用者的真實顯示器上執行滑鼠／鍵盤自動化腳本。

## 自訂 layout、keys、設定與 mod

先讀 app home 內生成的 `AGENTS.md`，再按功能讀 `herdr.d.ts`、`gpui-kit.d.ts`。這些是該版本的 API 文件，不編輯它們；app 啟動時會重寫。使用者偏好設定位於 app home 的 `settings.json`（如 `terminal_line_height`、`cell_width`、`scroll_speed`、`worktrees.directory`），未經要求不修改。

- 新版 app mods 是 `mods/<name>/main.js`，可有 `mod.json`（需接收鍵盤焦點時設 `"focusable": true`）；不要沿用 Sheprd 的 Luau mod API。
- 內建 mod 先 `mod.clone`，內建 layout 先 `layout.clone`；只改個人副本，參數查 `commands`。
- mods 是有程式、檔案與網路能力的受信任 JavaScript，不是 sandbox。先讀第三方程式再執行，不因能 check 就直接載入來源不明的 mod。
- `herdr-app check` 期間 `exec`、`hc`、`writeTextFile` 仍會執行；在 `init()` 中務必以 `checking()` 守衛副作用操作。
- 使用 `watchFolder(dir)` 監看目錄時，釋放資源需呼叫 `.cancel()`；`View.close(cx)` 在 app 結束時僅有 250ms 預算，勿執行慢速同步清理。
- save 會重新載入；失敗時保留舊畫面，畫面正常不能證明改動有效。
- 使用 theme colors。不要跨 callback 保存 `cx`；需要動畫用 native animation，不以重繪 timer 維持畫面。

修改後依序執行：

```text
herdr-app check /path/to/app-home/mods/my-panel
herdr-app status
herdr-app shot /path/to/workspace/herdr-mod.png
```

每步檢查退出碼。`check` 只載入並 render 一次，不證明互動正確；通過後實際開圖，檢查版面及本次相關互動。`shot` 預設輸出為當前目錄的 `herdr-shot.png`，抓取 app 最後一個已繪製 frame（即使視窗被遮擋或最小化仍可擷取，但前提是已有繪製過的視窗）。使用者未要求 UI 修改時，不為驗證技能而改其版面。

`herdr-app feedback <text>` 僅草擬回報，由使用者檢視並送出；不要誤報已提交。app 更新、core 更新與回報問題都是不同操作，依實際需求執行。
