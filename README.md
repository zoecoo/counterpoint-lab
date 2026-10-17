# Counterpoint Lab

無建置步驟的靜態音樂實驗室，全部功能在單一 `index.html` 內。直接開啟檔案，或透過 GitHub Pages 使用。

Steve Reich 啟發的生成式對位：選擇主音與十一種調式（五聲、教會調式、和聲／旋律小調），調整 BPM、聲部數（2–8）、固定聲部位移與累積速度。「生成新旋律」建立新的節奏素材；更換主音或調式時立即重新映射音高、保留節奏。勾選「逐步加入音符」時，每經過設定的循環數增加一音，完成後重新開始。

聲部矩陣以色塊顯示各聲部的發音位置，播放時標示目前拍點。此模型是固定聲部位移的卡農，並非連續的 Phasing。

聲部矩陣可直接編輯：點擊色塊切換音符／休止，每列可靜音或移除。調整聲部數或位移會重建基礎聲部並保留衍生聲部。力度滑桿同步影響音訊音量與 MIDI velocity。

Resultant Pattern Composer 從未靜音聲部的交錯中抽取高音、低音、最近音高與輪替線條，去除重複後依旋律連貫、重音與覆蓋度評分排序（啟發式，非確定的聽覺預測）。可逐一試聽、手動加入或自動選取最高分者；加入後可繼續分析衍生，上限 16 聲部。

頁面頂端內嵌一段預先生成的示範 MP3（使用原生 `<audio>` 播放器），用來在不依賴 Web Audio 的情況下先確認裝置可出聲；互動播放需由使用者點擊啟動 AudioContext。

MIDI 匯出為 Standard MIDI File format 1、480 PPQ，含速度軌與各聲部軌，依目前的累積設定輸出完整建構過程。

GitHub Actions workflow 將 main 部署至 GitHub Pages，repository Pages source 應設定為 GitHub Actions。
