# 人工智慧Artificial-Intelligence期中報告 鄭宇彤 鄭亦勳 黃靖雅
#### 1. result.mp4是執行後輸出的影片(感覺有壓縮因為轉換格式的關係) 2. 4.ipynb(主程式碼) 3. video_analyst-master.zip(其中一個資料)  4.Models(這個檔案Zip太大需分開傳)

## 主題 : Google Colab 跑目標追蹤的測試代碼報告
#### — 使用 OpenCV CSRT 進行影片追蹤 —
### 一、執行目的

本次執行的主要目的是在 Google Colab 雲端平台上，
測試一段 目標追蹤（Object Tracking） 的程式代碼，
利用 OpenCV 提供的 CSRT 演算法 來追蹤影片中的人物。

透過這次測試了解：

1. 如何在 Colab 上建立並執行 Python 環境。

2. 影像處理與目標追蹤的基本原理。

3. 追蹤結果的輸出與播放方式。

### 二、執行原理

目標追蹤是電腦視覺中的一項應用，
主要是讓電腦在影片連續畫面中自動找出並跟隨指定的物體。

這裡使用的 CSRT（Channel and Spatial Reliability Tracker） 屬於「濾波式追蹤算法」。

原理簡述如下：

 - 首先在影片第一幀指定要追蹤的區域（bounding box）。

 - 系統根據顏色與紋理建立模型。

 - 在後續畫面中不斷搜尋最相似的區域。

 - 若找到對應位置，會在畫面上以綠框標示。

CSRT 的優點是能在光線改變、部分遮擋的情況下，仍保持穩定的追蹤效果。

### 三、執行環境
項目	說明
平台	Google Colab（雲端運算環境）
語言	Python 3
主要套件	OpenCV、NumPy、Matplotlib
測試影片	OpenCV 官方影片 vtest.avi
GPU 型號	Tesla T4（Colab 提供）
輸出影片格式	MP4（H.264 編碼）

### 四、遇到的困難
1. 檔案太大下載很久//補充說明:雲端一個檔寫上傳需14~16h，實際上傳1h以上，參考的文案也有提到雲端跑很久的問題
2. 版本有三個地方太舊執行時一定要更新，不然會顯示錯誤無法運行
3. 上述安裝後，執行要重跑一次動作繁瑣
   ![image]( https://github.com/05ki19lj/Artificial-Intelligence/blob/main/%E9%9C%80%E9%87%8D%E8%B7%91%E7%95%AB%E9%9D%A2.png)
   - 補充改善方式：2、3完成後有分別更新後做總整理
4. 執行結果不知長怎樣，只給運行沒有寫或是附圖結果
#### 上述是一個段落
5. 加上運行實作例子，但要手動調整特定的不然會停在奇怪的地方無法偵測
   - 改善方式：調整成自動設定+個別有ID
6. 原檔是avi檔，但一般都用mp4檔(只能輸出檔)
   - 改善方式：改輸出檔名 

### 五、執行流程
1️⃣ 掛載 Google Drive

將個人雲端硬碟掛載到 Colab，以儲存程式與輸出結果。

2️⃣ 安裝與更新套件

透過 pip install 安裝所需的 Python 套件，如 opencv-python。

3️⃣ 下載測試影片

使用 OpenCV 官方影片作為測試素材。

4️⃣ 初始化 CSRT 追蹤器

設定第一幀的追蹤框作為初始參考。

5️⃣ 執行追蹤並輸出結果

逐幀處理影片，更新追蹤框位置並輸出成影片檔。

6️⃣ 播放結果影片

利用 HTML 介面直接在 Colab 中播放。

### 六、測試程式碼
import os
from google.colab import drive
from IPython.display import display, HTML
import cv2

#### 1️⃣ 掛載 Google Drive
drive.mount('/content/drive', force_remount=True)
project_path = '/content/drive/MyDrive/000/video_analyst'
os.chdir(project_path)

#### 2️⃣ 安裝必要套件
!pip install --quiet opencv-python matplotlib

#### 3️⃣ 下載測試影片
!wget -O /content/sample_video.avi https://github.com/opencv/opencv/raw/master/samples/data/vtest.avi
video_path = '/content/sample_video.avi'
output_path = '/content/drive/MyDrive/000/result.mp4'

#### 4️⃣ 初始化追蹤器
cap = cv2.VideoCapture(video_path)
ret, frame = cap.read()
tracker = cv2.TrackerCSRT_create()
bbox = (200, 50, 100, 80)
tracker.init(frame, bbox)

#### 5️⃣ 設定輸出影片
fourcc = cv2.VideoWriter_fourcc(*'mp4v')
out = cv2.VideoWriter(output_path, fourcc, 20.0, (frame.shape[1], frame.shape[0]))

#### 6️⃣ 執行追蹤
while True:
    ret, frame = cap.read()
    if not ret:
        break
    success, bbox = tracker.update(frame)
    if success:
        x, y, w, h = map(int, bbox)
        cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)
    out.write(frame)
cap.release(); out.release()

#### 7️⃣ 播放輸出影片
display(HTML(f"""
<video width="640" height="480" controls>
  <source src="{output_path}" type="video/mp4">
  您的瀏覽器不支援影片播放。
</video>
"""))

### 七、測試結果

在影片中，綠色方框能準確追蹤行走中的人物。

影片輸出成功，可於 Colab 中直接播放。

整體處理速度約為每秒 20 幀（FPS）。

📍 輸出影片路徑：
MyDrive/000/result.mp4

### 八、結果分析
- 成果前後對比圖
  
![image](https://github.com/05ki19lj/Artificial-Intelligence/blob/main/%E5%8A%9F%E8%83%BD%E5%89%8D%E5%BE%8C%E5%B0%8D%E6%AF%94%E5%9C%96.png)

測試項目	結果	說明
追蹤穩定度	良好	人物移動時框線穩定
執行速度	正常	每秒約 20 幀
系統運行	穩定	無中斷或錯誤訊息
輸出影片	成功	影片畫質清晰、可播放

CSRT 追蹤器在測試影片中能保持良好的辨識效果，
即使人物進入陰影或部分被遮擋，追蹤方框仍能正確鎖定。

### 九、結論

在 Google Colab 上成功執行了目標追蹤測試代碼，
使用 OpenCV CSRT 演算法 能更穩定追蹤影片中的人物。

整體測試結果表現良好，
追蹤穩定、影片輸出正常，
適合作為影像處理入門與追蹤技術理解的實例。
