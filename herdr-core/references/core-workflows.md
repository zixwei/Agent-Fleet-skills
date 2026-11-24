# Core 工作流程

以下 `hc` 代表已確認的 core CLI。命令為範例，執行前替換路徑及目標；PowerShell 呼叫存放於變數的執行檔用 `& $coreCli`，環境變數用 `$env:名稱`。外部命令後立即檢查 `$LASTEXITCODE`。

## 探索與啟動

```text
hc --version
hc server status
hc templates
hc schema
hc ls --json
hc get t7 --json
hc run --help
```

`ls` 包含其他人的工作，只讀取與任務相關的終端。地址可為 `t7`、自訂名稱（1–32 字元，僅限 `a-z 0-9 - _`）或 `machine/t7`。保留遠端完整地址，不能只取最後一段。`ls --json` 的 `cwd` 為啟動目錄，目前前景目錄以 `get --json` 的 `foreground.cwd` 為準。除了 `run`、`exec`、`read`、`file read` 之外，輸出被導向（piped）時預設為 JSON。

```text
hc run --bg --name review --cwd /path/to/repo reviewer "只審查 src/x.rs，唯讀，不建立子代理；將發現寫入指定檔案並回報路徑。"
hc run --bg --name tests --cwd /path/to/repo cargo test
hc run --bg --rm --no-notify --cwd /path/to/repo cargo build
```

會阻塞 shell 工具的慢指令可用 `hc run --bg --rm --no-notify` 卸載（需讀輸出時勿加 `--rm`）。`reviewer` 僅在 templates 確實存在且符合任務時使用；否則用已安裝的互動代理，明確指定適用的模型與參數。父代理已在 core 終端內時，可把其實際地址寫入首個任務，要求完成或受阻時以 `hc send <parent> <result>` 回報（父端會收到 `<hc from="...">` 標記的報告；長輸出應請子代理寫入檔案並只傳回路徑）。不讓子代理再建立代理。

子終端預設隨父程式結束；只有確實需要獨立存活才用 `--outlive-parent`。必要時可用 `--parent <addr>` 指定父終端或 `--no-parent` 解除父子關聯；沒有父 terminal 的外部工具不能期待 `wait --children` 自動識別本次工作，改等 launch 回傳的個別地址。

啟動後檢查回傳地址與 `first_message.sent`；false 時用 `get/read` 查 reason 與畫面，先解決就緒狀態再送任務。未知的 trust／permission 提示不能自動批准。

## 傳訊、等待與收成果

```text
hc send review "只重新檢查 src/x.rs 的修正"
hc send review --wait --if idle "請摘要結果"
hc keys review Enter
hc put review /path/to/context.md
hc label review role=reviewer
hc wait review --timeout 60
hc read review --lines 100
hc wait --children any --timeout 60
hc wait --children all --timeout 60
hc events
hc interrupt review
```

- `send` 會貼上文字並按 Enter；會影響目標程式，不是唯讀查詢。可用 `send --wait` 等待傳訊就緒，或 `send --if <idle|working>` 依狀態條件送出。`put <TERMINAL> <FILE>` 將本機檔案傳到目標終端旁（含遠端主機）並印出落地路徑、`keys` 送出按鍵序列、`label` 管理標籤（`key=` 可移除該鍵）。
- `wait` 預設逾時為 180 秒；`message` 表示訊息會送到父代理輸入，不是再用 read 擷取的訊息；先處理訊息，尚有依賴再等。
- `timeout` 是等待期限，不等於工作失敗；用同一地址繼續等，不重複啟動。前景 `run` 的 124 也可能只是等待逾時，程式仍在執行。
- `not_started` 或傳訊結果不明時先讀，不立即重送。
- `wait --output` 只匹配開始等待後的新輸出；不要用它等待已印過的 ready 字串。
- `read --lines 0` 讀可見畫面。working／blocked／error 時歷史讀取可能只回畫面並標記 incomplete，不能當完整 log。若設定 `restore_screen = true`，恢復後的畫面內容也會出現在 `hc read` 輸出中。
- 狀態由 OSC 7501 回報；一般程式可能沒有 agent status，wait 通常等到退出。Windows／PowerShell 返回 shell 時 OSC 7501 狀態目前不會自動清除。不要由畫面文字猜代理狀態。
- `hc events` 可串流狀態與生命週期事件；`hc schema` 可查目前版本的輸出結構。

取得實際結果後，才結束本次建立且不再需要的終端；`kill` 結束程式，`rm` 移除已結束終端。需要讀輸出就不要提前使用自動移除的 `--rm`。

## 遠端

```text
hc machine ls --json
hc run --bg --on vps --cwd /srv/app cargo test
hc exec --on vps --cwd /srv/app git diff
hc exec -i --on vps --cwd /srv/app sh -c "cat > /tmp/in.txt"
hc file read --on vps /srv/app/src/x.rs --offset 1 --limit 100
hc file edit --on vps /srv/app/src/x.rs --old "unique old text" --new "replacement"
```

`exec` 直接傳 argv，不經 shell（需傳 stdin 時加 `-i`）；需要 shell 語法時明確選目標主機允許的 shell。不要以本機檔案工具修改遠端路徑。`file edit` 要求舊文字唯一匹配，多組替換全部成功或全部失敗；`file write` 則覆寫整檔。

新增 machine、安裝遠端 core 或更新 core 是變更，不是連線診斷的必然步驟。登入狀態為 `needs_login` 時先說明需要的登入動作；Linux 的 logout 持續運行能力以 `keeps_running_after_logout` 為準，null 代表未知。Windows 上 `--via` 採 `CommandLineToArgvW` 切分參數（例如透過 WSL 連線時需留意引號規則）。

## 設定、恢復與錯誤

設定檔位於 `~/.config/herdr-core/config.toml`（Windows：`%APPDATA%\herdr-core\config.toml`）。常見設定如 `draft_wait`（預設 60 秒）與 `restore_screen`（設為 `true` 時恢復畫面會出現在 `hc read`）。Windows 上 `hc server status` 絕不會自動啟動 core。

`keep` 在程式遺失後恢復 shell，不會重跑原任務或自動續接代理。core 的正常更新／restart 與主機重開機不能視為相同恢復情境；僅在隔離測試或使用者明確要求時才使用 `hc server restart --fresh`。

錯誤通常在 stderr 的 `{error: {kind, message, hint}}`；以實際訊息與 help 判讀。常見退出碼：3 找不到目標、7 需要目標、9 blocked、10 status 不符、11 已結束。取得新證據後才調整方法，不原封重跑。

若需測試 core 的啟停或恢復，使用獨立 `HC_SESSION` 與隔離設定／資料目錄，不碰使用者的既有 core。
