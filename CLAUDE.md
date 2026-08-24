# travel-itinerary-planner

這個 repo 是一個 **Claude skill 的原始碼**,不是應用程式。`travel-itinerary-planner/` 就是要打包上傳到 claude.ai 的資料夾。skill 的用途:差旅／旅遊行程規劃,問卷蒐集後排出行程,產出比旅行社行程單更詳細的 HTML 旅遊手冊。

## 最重要的一件事:這個 skill 的執行環境不是這裡

SKILL.md 與 references 裡引用的工具與路徑全部是 **claude.ai 聊天介面專屬,在 Claude Code 不存在**:

`ask_user_input_v0`、`places_search`、`places_map_display_v0`、`weather_fetch`、`memory_read` / `memory_write` / `memory_list` / `memory_str_replace`、`present_files`、`view`、`create_file`、`/mnt/skills/public/frontend-design/SKILL.md`、`/mnt/user-data/outputs/`

**它們是對的。不要因為在這個工作區找不到,就把它們當成錯誤而移除或改寫。** 這是這個 repo 最容易犯的錯。

因此:
- 在 Claude Code 只能**編修檔案**(純文字工作)
- **無法**實跑問卷互動、設定檔套用、HTML 產出 —— 要測試必須打包上傳到 claude.ai 聊天介面

## 檔案分工

| 檔案 | 職責 | skill 執行時何時讀 |
|---|---|---|
| `travel-itinerary-planner/SKILL.md` | 流程 Step 0–8 | 永遠載入 |
| `references/intake-questions.md` | 問卷題庫 A–H、A-2 事由分支、設定檔儲存格式 | Step 2、Step 3 |
| `references/research-and-scheduling.md` | 資料查證規則、排程七步、限制回核 | Step 4,排每一天之前 |
| `references/html-output-spec.md` | 14 個必含區塊的欄位規格、兩階段產出 | Step 5、Step 8 |
| `references/editorial-standards.md` | 編輯判斷、推薦理由寫法、用字與專有名詞 | Step 5,寫內容時 |
| `references/output-checklist.md` | 硬擋 15 項 + 完整驗收清單(131 項) | Step 6,交付前 |

## 編修守則

**規則的權威來源是 reference 檔,不是 SKILL.md。** SKILL.md 只重述歷史上最常被違反的少數規則(讓永遠載入的部分帶著最關鍵的約束),其餘一律以 reference 檔為準。改規則要改 reference,再回頭檢查有沒有別處重述同一條 —— 已知的重述清單在 `README.md`。

**這個 repo 歷史上重複發生過的錯誤是「改一端忘了另一端」,發生過三次:**

1. 加或改**輸出要求**(spec、editorial)之後,回頭檢查 intake 有沒有收集到那個資訊。否則會出現「輸出承諾了問卷沒問的東西」。
2. 加或改**輸入題目**(intake)之後,檢查輸出端有沒有落腳處、排程階段會不會真的用到。否則會出現「問卷收集了但完全沒用上」。
3. 加新的**入口或情境**之後,回頭補 frontmatter `description` 的觸發詞(上限 1024,目前約 976)。否則寫了流程但 skill 根本進不來。

其他約定:
- 跨檔引用一律用**描述性寫法**(「見編輯標準的主題附錄一節」),不要寫「第五節」—— 節一調整就過時,已經發生兩次
- 加規則之前先想清楚它會不會讓問卷輪次或驗收清單膨脹到被跳著看。題庫已 ~80 項、清單已 131 項,**再加東西要同時說明怎麼維持可用**
- 檔案一律 UTF-8 無 BOM。改完用 `python` 檢查 `chr(0xFFFD)` 與 BOM

## 打包

```bash
python -c "
import zipfile, os
with zipfile.ZipFile('travel-itinerary-planner.zip','w',zipfile.ZIP_DEFLATED) as z:
    for r,d,fs in os.walk('travel-itinerary-planner'):
        for f in sorted(fs): z.write(os.path.join(r,f), os.path.join(r,f).replace(os.sep,'/'))
"
```

**不要用 Windows PowerShell 5.1 的 `Compress-Archive`** —— 它把路徑分隔符寫成反斜線,部分解壓工具會把整個路徑當成一個檔名,導致 `references/` 子目錄結構丟失。

## 已否決的方向(不要再提)

- **主行程每個時段並列 2-3 個候選** → 已改為「單一推薦定案 + 備案獨立成章並標明可替換哪一段」。當初的問題不是「一個 vs 三個」,是「單一定案卻沒有備案」
- **一次產出詳細版與列印版兩份** → 已改為漸進式。內容還在改的階段維護兩份,改一個時間要改兩個檔案
- **設定檔自動套用** → 已改為逐項詢問式套用
- **只在 B 類提醒「同行者需求可能不同」** → 已提升為 B–H 全類別通用原則
- **放一份填了假資料的範例 HTML 當品質基準** → 會把捏造的班號店名寫進 skill,違反 skill 自己的規則;只放佔位符的空模板又無法示範密度且帶來樣板感。改用「單一時間軸項目的完整範例」+ 列印 CSS 片段
- **照片與自行找圖** → 一律不自行找圖或嵌外部圖片(離線失效、多半禁止 hotlink),使用者主動提供檔案時才放。使用者已明確表示照片不必,重點在文字面與行程完整度

## `ref/`(品質基準,未進 git)

使用者提供的兩份上海行程 PDF,是「至少要做到這個程度」的底標,體積 21MB 所以沒進 git:

- `上海行程_完整指南.pdf` —— 14 頁、15,003 字、0 張圖。p.1 五天完整時間軸壓在一頁,後面每頁 2 個項目的深度介紹,再接備案 3 頁、住宿 1 頁
- `上海五日手冊_B5.pdf` —— 34 頁、約 80 張圖、18.7MB。一個項目一頁,有頁碼頁眉頁腳與附錄

要讀內容得先 `python -m pip install pypdf`(預設沒裝,也沒有 poppler,Read 工具無法直接讀 PDF)。

skill 的欄位覆蓋已超過這兩份(它們沒有緊急頁、行前清單、訂位追蹤表、費用總估),ref 的強項在編輯判斷與敘事密度,那部分已寫進 `editorial-standards.md`。

## 現況與下一步

紙上 review 已收斂(最後幾個 commit 是編號與引用衛生,不是實質內容)。**尚未實跑過任何一次。** 三個具體的懷疑點只有實測才驗得出來:

1. 131 項的驗收清單會不會還是被跳著看
2. 排程七步會不會被壓縮成「憑感覺排,然後回頭補理由」
3. 限制回核會不會變成打勾儀式,沒有真的逐項對

建議用跟 ref 同一個情境實跑(上海五日、CIFF 傢俱展、包車、華爾道夫五星),直接跟那兩份 PDF 對照。
