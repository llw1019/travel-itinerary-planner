# travel-itinerary-planner

給 claude.ai 聊天介面環境使用的差旅／旅遊行程規劃 skill。這個 repo 是 skill 的原始碼,`travel-itinerary-planner/` 就是要打包上傳的資料夾。

## 打包與上傳

```bash
python -c "
import zipfile, os
with zipfile.ZipFile('travel-itinerary-planner.zip','w',zipfile.ZIP_DEFLATED) as z:
    for r,d,fs in os.walk('travel-itinerary-planner'):
        for f in sorted(fs): z.write(os.path.join(r,f), os.path.join(r,f).replace(os.sep,'/'))
"
```

不要用 Windows PowerShell 5.1 的 `Compress-Archive`,它會把路徑分隔符寫成反斜線,部分解壓工具會把整個路徑當成一個檔名,導致 `references/` 子目錄結構丟失。

## 檔案分工

| 檔案 | 職責 | 什麼時候讀 |
|---|---|---|
| `SKILL.md` | 流程(Step 0–8) | 永遠載入 |
| `references/intake-questions.md` | 問卷題庫 A–H、設定檔儲存格式 | Step 2、Step 3 |
| `references/research-and-scheduling.md` | 資料查證規則、排程方法 | Step 4,排每一天之前 |
| `references/html-output-spec.md` | 14 個必含區塊的欄位規格、兩階段產出 | Step 5、Step 8 |
| `references/editorial-standards.md` | 編輯判斷、用字與專有名詞 | Step 5,寫內容時 |
| `references/output-checklist.md` | 硬擋 13 項 + 完整驗收清單 | Step 6,交付前 |

## 編修這個 skill 時要注意

**規則的權威來源是 reference 檔,不是 SKILL.md。** SKILL.md 只重述歷史上最常被違反的少數規則(讓永遠載入的部分帶著最關鍵的約束),其餘一律以 reference 檔為準。

因此改規則時要**改 reference 檔,然後檢查有沒有其他檔案重述了同一條**。目前已知有重述、改動時要一起同步的規則:

| 規則 | 出現在 |
|---|---|
| 步調密度(緊湊 5-6／適中 4-5／悠閒 2-3) | SKILL.md、research-and-scheduling.md、output-checklist.md |
| 營業時間與休館日查核 | SKILL.md、research-and-scheduling.md、html-output-spec.md、editorial-standards.md、output-checklist.md |
| 主行程單一定案 + 備案獨立成章 | SKILL.md、html-output-spec.md、output-checklist.md |
| 移動獨立成行 | SKILL.md、research-and-scheduling.md、html-output-spec.md、output-checklist.md |
| 天氣:預報 vs 歷史氣候 | SKILL.md、research-and-scheduling.md、output-checklist.md |

另外:`SKILL.md` 的 frontmatter `description` 是 skill 的觸發條件。新增入口或情境(例如「核對既有行程」)時,要同步在 description 補對應的觸發詞,否則寫了流程但 skill 進不來。上限 1024 字元,目前約 976,加東西前先量。

## 環境限制

這個 skill 引用的工具(`ask_user_input_v0`、`places_search`、`places_map_display_v0`、`weather_fetch`、`memory_*`、`present_files`)與路徑(`/mnt/skills/public/frontend-design/SKILL.md`、`/mnt/user-data/outputs/`)都是 claude.ai 聊天介面專屬。在 Claude Code 只能編修檔案,無法實跑問卷、設定檔套用或 HTML 產出。

照片一律不自行找圖或嵌外部圖片(離線會失效,而且多半禁止 hotlink),使用者主動提供檔案時才放。
