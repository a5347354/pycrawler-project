# Crawler Exam

## 實作方法介紹
<p>原本採用Seleinum抓資料回來，後來覺得太過費時，加上Coupy的前端感覺是採用互動式彈出資料，想說用Chrome開發人員工具觀察前端與伺服器的溝通方式，發現前端與伺服器GET API取得JSON資料，一次30筆(len=30)，從startNewsID這個參數當作起始</p>

![Chrome_Developer_Tool](/pictures/Chrome_Developer_Tool.png)

<br>若要取得500筆資料url就是
<br>https://www.cupoy.com/MixNewsMongoAction.do?op=getTopMixNewsByBucketGroup&groupid=business_tw&startNewsID=1&len=500
<br>看起來也不用另外帶header（將這串丟進瀏覽器的網址列）
![json](/pictures/json.png)
但資料太多json有點亂，把len條小，並用json Editor Online整理一下
![json_editor_online](/pictures/json_editor_online.png)

<p>知道大概的json結構後，用request和json把資料抓下來，確定一下是否符合預期的比數</p>

![request](/pictures/request.png)

<p>將資料塞進List，以便進行之後的利用，這邊有點小問題，後來執行發現程式掛了，才發現有筆資料竟然沒有socialScorer這個json的key，只好選擇跳過那筆</p>

![convert_to_list](/pictures/convert_to_list.png)

<p>觀察資料時，發現到title中會有發佈者，這會影響到後續計算詞頻時的結果，先將發佈者拿出來，並從title中剔除。</p>
<p>這邊用正規表達式做切割，再取得最後一個Array，就是發佈者，把第一個array到倒數第三個串(append)起來就是title了</p>

![get_publisher_from_title](/pictures/get_publisher_from_title.png)
![remove_publisher_from_title](/pictures/remove_publisher_from_title.png)

<p>再把它轉成dataframe，後來發現有些沒有發佈者，這邊選擇直接濾掉該筆</p>

![convert_to_dataframe](/pictures/convert_to_dataframe.png)

<p>下一步計算dfitf，jieba需要字詞庫，來進行分詞，雖然詞庫是免費的，但堪用，在計算dfitf的權重列出最多的40筆</p>

![caculate_tfidf](/pictures/caculate_tfidf.png)

<p>為了手動計算字詞頻率，必須要將stop_words過濾掉，像是「什麼」之類的，因為title有些特別的標點符號，在這順便加入到stop_words中。(stop_words的詞庫也是上網找的)</p>

![remove_stop_words](/pictures/remove_stop_words.png)

<p>計算字詞的頻率，採用分組統計並加總，就可針對字詞計數</p>

![count_freq](/pictures/count_freq.png)

<p>有了詞頻，就可以用world_cloud將文字雲畫出來，這邊要注意，因為文字雲需要中文字的字型，不然會出現亂碼，所以隨便載了個免費的來使用</p>

![world_cloud](/pictures/world_cloud.png)
