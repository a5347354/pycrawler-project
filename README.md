# Crawler Exam

## 實作方法介紹
原本採用Seleinum抓資料回來，後來覺得太過費時，加上Coupy的前端感覺是採用互動式彈出資料，想說用Chrome開發人員工具觀察前端與伺服器的溝通方式，發現前端與伺服器GET API取得JSON資料，一次30筆(len=30)，從startNewsID這個參數當作起始
![Chrome_Developer_Tool](/pictures/Chrome_Developer_Tool.png)
若要取得500筆資料url就是
<br>https://www.cupoy.com/MixNewsMongoAction.do?op=getTopMixNewsByBucketGroup&groupid=business_tw&startNewsID=1&len=500
<br>看起來也不用另外帶header（將這串丟進瀏覽器的網址列）
![json](/pictures/json.png)
但資料太多json有點亂，把len條小，並用json Editor Online整理一下
![json_editor_online](/pictures/json_editor_online.png)

