# Counterpoint Lab

無建置步驟的靜態音樂實驗室。直接開啟 index.html，或透過 GitHub Pages 使用。

選擇主音、調式、BPM、起始聲部數與固定時間位移，再按「重新生成」。參數中的 BPM、力度與累積開關即時生效，其餘在重新生成時套用。矩陣每格為十六分音符；點擊可切換休止與主音。每輪增加兩格，八輪後完整循環。

Resultant Pattern Composer 從未靜音的聲部抽取高音、低音、最近音高與輪替線條，去除重複後依旋律連貫、重音與覆蓋度排序。分數是啟發式，不是確定的聽覺預測。手動試聽或自動選取後可繼續衍生，最多 16 聲部。沒有聲音時不產生候選。

播放必須由使用者點擊啟動 AudioContext，並等待 resume 完成；切換到背景自動停止。iPhone Safari 請保持頁面在前景並確認音量。桌面 WebKit 測試不能取代實體 iPhone 驗證。

MIDI 為 Standard MIDI File format 1、480 PPQ，包含速度軌與各未靜音聲部。累積模式輸出八輪，否則輸出一輪；力度控制同步套用於音訊與 MIDI。

GitHub Actions workflow 將 main 部署至 GitHub Pages，repository Pages source 應設定為 GitHub Actions。
