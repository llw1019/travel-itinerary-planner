# travel-itinerary-planner

差旅／旅遊行程規劃的 Claude skill 原始碼。`travel-itinerary-planner/` 是要打包上傳到 claude.ai 的資料夾。

**工作約定、環境限制、已否決的方向、打包指令都在 [CLAUDE.md](CLAUDE.md)。** 這份 README 只放一件 CLAUDE.md 不放的東西:規則重述的同步表(它太長,不適合放進每個 session 都會載入的檔案)。

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
