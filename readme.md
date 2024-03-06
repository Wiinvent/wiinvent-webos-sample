SDK:

````javascript
<script src="https://wiinvent.tv/sdk/tv/wii-sdk-1.6.8.js"></script>
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
    hls: hls,
    player: video,
    srcVideo: src,
    channelId: "2",
    streamId: "1999",
    partnerSkipOffset: 6,
    vastLoadTimeout: 10,
    mediaLoadTimeout: 10,
    bufferingVideoTimeout: 15,
    alwaysCustomSkip: true,
    isAutoRequestFocus: false,
    bitrate: 1024,
    skipText: "Skip ads after {0} seconds",
    skippableText: "Skip ads"
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
      "All_ADS_COMPLETE",
    ].includes(e.data.type)) {
    console.log('mmmm', e)
  }
})

```

2. Parameter

| Key                   | Description                                               |     Type |
|:----------------------|:----------------------------------------------------------|---------:|
| tenantId              | Identifier of the account created by the Broadcast Center |  integer |
| channelId             | Identifier of the channel created by partner              |   string |
| streamId              | Identifier of the channel created by partner              |   string |
| vastLoadTimeout       | Vast Load Timeout                                         |  integer |
| mediaLoadTimeout      | Media Load Timeout                                        |  integer |
| bufferingVideoTimeout | Buffering Video Timeout                                   |  integer |                                  
| partnerSkipOffset     | Skip Ad Duration                                          |  integer |                                  
| env                   | Override the API host url for development and testing     | constant |
| deviceType            | Device Type                                               | constant |
| thirdPartyToken       | JWT Token from partner                                    |   string |
| playerType            | Player Type                                               | constant |
| alwaysCustomSkip      | Decided to use custom skip button                         |  boolean |
| isAutoRequestFocus    | Decided to focus on skip button after skip time           |  boolean |
| bitrate               | Bitrate                                                   |   number |
| skipText              | Text show on skip button when time count                  |   string |
| skippableText         | Text show on skip button when can skip                    |   string |

3. Constant

| Key         | Description                                                                                      |     
|:------------|:-------------------------------------------------------------------------------------------------|
| playerType  | WI.PlayerType.VIDEO_JS <br> WI.PlayerType.SHAKA <br> WI.PlayerType.HLS <br/>WI.PlayerType.AKAMAI |  
| deviceType  | WI.DeviceType.TV <br/> WI.DeviceType.WEB                                                         |  
| env         | WI.Environment.SANDBOX <br/> WI.Environment.PRODUCTION                                           |   
| contentType | WI.ContentType.VOD <br/>WI.ContentType.LIVESTREAM                                                | 

4. Ads Callback

| Type      | Value      | Description                                   |
|:----------|:-----------|:----------------------------------------------|
| EventType | REQUEST    | Fired when the ad requests                    |
| EventType | LOADED     | Fired when the ad loaded                      |
| EventType | START      | Fired when the ad starts playing              |
| EventType | IMPRESSION | Fired when the impression URL has been pinged |
| EventType | CLICK      | Fired when the ad is clicked                  |
| EventType | COMPLETE   | Fired when the ad completes playing           |
| EventType | SKIPPED    | Fired when the ad is skipped by the user      |
| EventType | ERROR      | Fired when the ad has an error                |
| EventType | DESTROY    | Fires when the ad destroyed                   |

