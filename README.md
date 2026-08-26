# travel-itinerary-planner

差旅／旅遊行程規劃的 Claude skill。問卷蒐集資訊後排出行程,產出比旅行社行程單更詳細的 HTML 旅遊手冊。

## 立刻開始用

```bash
git clone <this-repo> && cd travel-itinerary-planner
claude
```

然後直接說你要什麼:

- 「幫我排三天東京」
- 「幫老闆排一趟上海出差,九月四號到八號,要去傢俱展」
- 「把這份行程做成手冊」/「幫我核對這份行程有沒有問題」

skill 在 `.claude/skills/travel-itinerary-planner/`,Claude Code 開在這個 repo 就會自動載入,不需要安裝或設定。**問卷需要互動,所以要用互動模式**(直接 `claude`,不是 `claude -p`)。

產出的行程手冊會存在 `itineraries/`,你的旅遊設定檔存在 `profiles/` —— 都在這個資料夾底下看得到,而且都已 `.gitignore`,不會被 commit 進 repo。

排行程過程中會做大量網頁查證(航班、營業時間、店家、天氣),第一次會遇到 `WebSearch` / `WebFetch` 的權限提示。這個 repo **刻意不預設任何權限允許清單** —— 要不要放行由你自己決定,可以選「本次允許」或加進你自己的設定。

同一份檔案也可以打包上傳到 claude.ai 聊天介面使用 —— 打包指令見 [CLAUDE.md](CLAUDE.md)。skill 內建環境對應表,兩邊的流程與品質標準相同,只有工具名稱不同。

**工作約定、環境限制、已否決的方向都在 [CLAUDE.md](CLAUDE.md)。** 這份 README 剩下的部分只放一件 CLAUDE.md 不放的東西:規則重述的同步表(它太長,不適合放進每個 session 都會載入的檔案)。

## 規則重述同步表

規則的權威來源是 reference 檔。但有些規則刻意在多處重述(讓永遠載入的 SKILL.md 帶著最關鍵的約束、讓驗收清單能獨立使用)。**改動下列任一條規則時,要一起檢查表格右欄的所有檔案:**

| 規則 | 出現在 |
|---|---|
| 步調密度(緊湊 5-6／適中 4-5／悠閒 2-3) | SKILL.md、research-and-scheduling.md、output-checklist.md |
| 營業時間與休館日查核(含查到但衝突要標矛盾) | SKILL.md、research-and-scheduling.md、html-output-spec.md、editorial-standards.md、output-checklist.md |
| 主行程單一定案 + 備案獨立成章 | SKILL.md、html-output-spec.md、output-checklist.md |
| 移動獨立成行 | SKILL.md、research-and-scheduling.md、html-output-spec.md、output-checklist.md |
| 天氣:預報 vs 歷史氣候 | SKILL.md、research-and-scheduling.md、output-checklist.md |
| 限制回核(飲食/無障礙/體力/保密/語言/價位) | SKILL.md、research-and-scheduling.md、output-checklist.md |
| 地圖連結的目的地適用性(中國大陸等不能用 Google Maps) | html-output-spec.md、output-checklist.md |
| 交付回覆要講的五件事 | html-output-spec.md、output-checklist.md |
| 未確定事項要寫具體的下一步動作,不能只標「待補」 | SKILL.md、html-output-spec.md、output-checklist.md |
| 硬擋 15 項(在完整清單裡刻意重列一次) | output-checklist.md 第零節與各分類節 |

另外,`output-checklist.md` 開頭寫著完整清單的項數,加減項目時要一起改那個數字。
