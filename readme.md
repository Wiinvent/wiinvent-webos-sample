SDK:

````javascript
<script src="https://wiinvent.tv/sdk/tv/wii-sdk-1.7.2.js"></script>
````

1. Code Instream Sample:

```javascript
var video = document.getElementById('video');
var src = "https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8"
var wiiSdk = []

if (Hls.isSupported()) {
  var hls = new Hls({
    enableWorker: true,
    debug: true,
  });
  wiiSdk = new WI.InstreamSdk({
    env: WI.Environment.SANDBOX,
    tenantId: 14,
    deviceType: WI.DeviceType.TV,
    domId: "videoId",
    player: video,
    srcVideo: src,
    channelId: "2", // danh sách id của category của nội dung & cách nhau bằng dấu ","    
    streamId: "1999", // id nội dung
    contentType: WI.ContentType.VIDEO, //content type FIRM | TV | VIDEO
    title: "noi dung 1", // tiêu đề nội dung
    transId: "111", //mã giao dịch tạo từ server đối tác - client liên hệ server để biết thêm thông tin
    category: "1, 2", // danh sach tiêu đề category của nội dung & cách nhau bằng dấu ","  
    keyword: "1, 2", //từ khoá nếu có | để "" nếu ko có
    age: "20", // tuổi , nếu không có thì để 0
    gender: WI.Gender.MALE, //giới tính nếu không có thì set NONE
    uid20: "", // unified id 2.0, nếu không có thì set ""
    partnerSkipOffset: 6,
    vastLoadTimeout: 10,
    mediaLoadTimeout: 10,
    bufferingVideoTimeout: 15,
    alwaysCustomSkip: true,
    isAutoRequestFocus: false,
    bitrate: 1024,
    skipText: "Skip ads after {0} seconds",
    skippableText: "Skip ads",
    isUsePartnerSkipButton: true
  })
  hls.loadSource(src);
  hls.attachMedia(video);
  video.muted = true;
  wiiSdk.start()
}

window.addEventListener("message", function (e) {
  if (
    [
      "REQUEST",
      "LOADED",
      "START",
      "PAUSED",
      "RESUMED",
      "ERROR",
      "PLAYER_ERROR",
      "CLICK",
      "IMPRESSION",
      "SKIPPED",
      "COMPLETE",
      "DESTROY",
      "FULLSCREEN",
      "END",
      "ALL_ADS_COMPLETE",
    ].includes(e.data.type)) {
    console.log('mmmm', e)
  }
})

```
2. Code Welcome Sample:

```javascript
var wiiSdk = [];

wiiSdk = new WI.WelcomeSdk({
  env: WI.Environment.SANDBOX,
  tenantId: 14,
  transId: "111", //mã giao dịch tạo từ server đối tác - client liên hệ server để biết thêm thông tin
  age: "20", // tuổi , nếu không có thì để 0
  gender: WI.Gender.MALE, //giới tính nếu không có thì set NONE
  uid20: "", // unified id 2.0, nếu không có thì set ""
  deviceType: WI.DeviceType.TV,
  partnerSkipOffset: 5,
  vastLoadTimeout: 10,
  mediaLoadTimeout: 10,
  bufferingVideoTimeout: 15,
  alwaysCustomSkip: true,
  isAutoRequestFocus: false,
  bitrate: 1024,
  skipText: "Skip ads after {0} seconds",
  skippableText: "Skip ads",
  isUsePartnerSkipButton: true
})
wiiSdk.start()

window.addEventListener("message", function (e) {
  if (
    [
      "HAVE_ADS",
      "NO_ADS",
      "REQUEST",
      "LOADED",
      "START",
      "PAUSED",
      "RESUMED",
      "ERROR",
      "PLAYER_ERROR",
      "CLICK",
      "IMPRESSION",
      "SKIPPED",
      "COMPLETE",
      "DESTROY",
      "FULLSCREEN",
      "END"
    ].includes(e.data.type)
  ) {
    console.log("mmmm", e);
  }
});

```
3. Parameter

| Key                   | Description                                                                       |     Type |
|:----------------------|:----------------------------------------------------------------------------------|---------:|
| tenantId              | Identifier of the account created by the Broadcast Center                         |  integer |
| channelId             | Danh sách id của category của nội dung & cách nhau bằng dấu ","                   |   string |
| streamId              | Id nội dung                                                                       |   string |
| vastLoadTimeout       | Vast Load Timeout                                                                 |  integer |
| mediaLoadTimeout      | Media Load Timeout                                                                |  integer |
| bufferingVideoTimeout | Buffering Video Timeout                                                           |  integer |                                  
| partnerSkipOffset     | Skip Ad Duration                                                                  |  integer |                                  
| env                   | Override the API host url for development and testing                             | constant |
| deviceType            | Device Type                                                                       | constant |
| thirdPartyToken       | JWT Token from partner                                                            |   string |
| playerType            | Player Type                                                                       | constant |
| alwaysCustomSkip      | Decided to use custom skip button                                                 |  boolean |
| isAutoRequestFocus    | Decided to focus on skip button after skip time                                   |  boolean |
| bitrate               | Bitrate                                                                           |   number |
| title                 | Tiêu đề của nội dung                                                              |   string |
| tranId                | Mã giao dịch tạo từ server đối tác - client liên hệ server để biết thêm thông tin |   string |
| category              | Danh sach tiêu đề category của nội dung & cách nhau bằng dấu ","                  |   string |
| keyword               | Từ khoá tìm kiếm của nội dung (nếu có)                                            |   string |
| age                   | Tuổi (Nếu có)                                                                     |   number |
| gender                | Giới tính (nếu có)                                                                | constant |
| uid20                 | Unified id 2.0 (nếu có)                                                           |   string |

4. Constant

| Key         | Description                                                                                      |     
|:------------|:-------------------------------------------------------------------------------------------------|
| playerType  | WI.PlayerType.VIDEO_JS <br> WI.PlayerType.SHAKA <br> WI.PlayerType.HLS <br/>WI.PlayerType.AKAMAI |  
| deviceType  | WI.DeviceType.TV <br/> WI.DeviceType.WEB                                                         |  
| env         | WI.Environment.SANDBOX <br/> WI.Environment.PRODUCTION                                           |   
| contentType | WI.ContentType.TV <br/>WI.ContentType.FILM <br/>WI.ContentType.VIDEO                             | 
| gender      | WI.Gender.MALE <br/>WI.Gender.FEMALE <br/>WI.Gender.OTHER <br/>WI.Gender.NONE                    | 

5. Ads Callback

| Type      | Value            | Description                                   |
|:----------|:-----------------|:----------------------------------------------|
| EventType | HAVE_ADS         | Fired when have welcome ad                    |
| EventType | NO_ADS           | Fired when not have welcome ad                |
| EventType | REQUEST          | Fired when the ad requests                    |
| EventType | LOADED           | Fired when the ad loaded                      |
| EventType | START            | Fired when the ad starts playing              |
| EventType | IMPRESSION       | Fired when the impression URL has been pinged |
| EventType | COMPLETE         | Fired when the ad completes playing           |
| EventType | SKIPPED          | Fired when the ad is skipped by the user      |
| EventType | ERROR            | Fired when the ad has an error                |
| EventType | DESTROY          | Fires when the ad destroyed                   |
| EventType | ALL_ADS_COMPLETE | Fires when no more ad will show up            |

