# Free Video

主要透過自己新增youtube播放清單，來避開影片型廣告的插入，方便聽音樂或者是觀看影片．

 

＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝**Project 連結**＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝

[free-video](https://cookieseventeen.github.io/Semi-automatic-youtube-player/)

＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝

## V1功能  提供單個影片，列表播放．

非常簡陋的提供一個input，讓使用者貼上清單，控制影片播放，從單個影片到可以播放整個列表影片的功能

![https://s3.us-west-2.amazonaws.com/secure.notion-static.com/269394bb-bb5a-4362-8048-770c82a87552/.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125431Z&X-Amz-Expires=86400&X-Amz-Signature=55def8733b62fc0f00ecbdba74fd4f0dd86ae5ef648b2c0e229b22be8f0680cd&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22.jpg%22](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/269394bb-bb5a-4362-8048-770c82a87552/.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125431Z&X-Amz-Expires=86400&X-Amz-Signature=55def8733b62fc0f00ecbdba74fd4f0dd86ae5ef648b2c0e229b22be8f0680cd&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22.jpg%22)

---

## V2版本 新增介面，提供播放清單控制．

V2版本主要解決第一版遇到的問題，原本只是想聽音樂而已所以不會想特別選擇播放哪首歌，但後來想再增加影片觀看的功能，所以不再只是隨機播放列表的內容，而是可以透過列表去控制，額外發現API能夠拿取的資料最大５０筆，所以還新增了播放列表頁面的控制。

更新UI介面，主要為黑色背景，減少螢幕使用電力．

![https://s3.us-west-2.amazonaws.com/secure.notion-static.com/917cd2e4-97c1-480d-bd34-2e2293c0f42f/.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125518Z&X-Amz-Expires=86400&X-Amz-Signature=53010f056ce5430e2b6e799a5267249af8bd034f7ef49aae9847f8a0c7d7a8d4&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22.jpg%22](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/917cd2e4-97c1-480d-bd34-2e2293c0f42f/.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125518Z&X-Amz-Expires=86400&X-Amz-Signature=53010f056ce5430e2b6e799a5267249af8bd034f7ef49aae9847f8a0c7d7a8d4&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22.jpg%22)

---

## V3 預計開發功能

１．播放清單的儲存，例如我已經新增的播放清單，希望可以透過一個page 點擊進入，已經進入播放器的，也有一個便捷側邊選單進行切換。

２．確認ＡＰＩ歸格，除了list，是不是還有其他載入視頻的方式。

３．分頁問題，目前分頁還是透過ＡＰＩ呼叫，所以如果我想自己隨機播放整個播放清單是不行的，又或者我撥到該清單的最後一首他不會跳最後一頁

４．資料庫串接，也可以Ｖ４板本在家入

＊5.免廣告播放器的研究，透過這個網址不用廣告

[https://www.youtube-nocookie.com/embed/0UPAV8g9ve4?rel=0&modestbranding=1&hd=1&showinfo=0&controls=1&iv_load_policy=3&wmode=transparent&autohide=1&autoplay=0](https://www.youtube-nocookie.com/embed/0UPAV8g9ve4?rel=0&modestbranding=1&hd=1&showinfo=0&controls=1&iv_load_policy=3&wmode=transparent&autohide=1&autoplay=0)

＊6.新版介面 如果取消分頁的情況，增加快速搜尋的欄位，讓自己可以更快的找到影片，像是下方得組合

![https://s3.us-west-2.amazonaws.com/secure.notion-static.com/98371449-9932-4556-8bbe-0297b583c031/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125622Z&X-Amz-Expires=86400&X-Amz-Signature=4da169993a7b476c1feaeff6134d4bf150eaad5af7fcc9686399024705e13ca9&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/98371449-9932-4556-8bbe-0297b583c031/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125622Z&X-Amz-Expires=86400&X-Amz-Signature=4da169993a7b476c1feaeff6134d4bf150eaad5af7fcc9686399024705e13ca9&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7b9df78f-c1a7-4ea2-b1af-8dfd6dc760e2/1626389859935.jpeg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125728Z&X-Amz-Expires=86400&X-Amz-Signature=2cf156c78ee63f59f420b2b399777a1c0253046725d68a83fc1dbf1d87f14af3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%221626389859935.jpeg%22](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7b9df78f-c1a7-4ea2-b1af-8dfd6dc760e2/1626389859935.jpeg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210823%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210823T125728Z&X-Amz-Expires=86400&X-Amz-Signature=2cf156c78ee63f59f420b2b399777a1c0253046725d68a83fc1dbf1d87f14af3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%221626389859935.jpeg%22)

[Youtube Music Player](https://www.notion.so/Youtube-Music-Player-8751c20ee9f1453d8f94c48c3f5b202a)

[LIST api](https://www.notion.so/LIST-api-2232ea1427094453a737e2fdc54389aa)
