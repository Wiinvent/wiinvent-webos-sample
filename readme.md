SDK:

````javascript
<script src="https://wiinvent.tv/sdk/tv/wii-sdk-2.0.35.js"></script>
````

1. Code Instream Sample:

```javascript
 var video = document.getElementById('video');
var playPauseBtn = document.getElementById('play-pause');
var adsContainer = document.getElementById('wiinvent_ads_container_id');
var controls = document.querySelector('.controls');
var skipButton = null;
var initAds = false;
var wiiSdk = null;

// Play/Pause functionality
playPauseBtn.addEventListener('click', () => {
  if (video.paused || video.ended) {
    video.play();
    playPauseBtn.textContent = 'Pause';
    handleAds();
    initAds = true;
  } else {
    video.pause();
    playPauseBtn.textContent = 'Play';
  }
});


function handleAds() {
  if (initAds) return;
  
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
        "NO_ADS"
      ].includes(e.data.type)
    ) {
      console.log("mmmm", e.data);
    }
    
    if (e.data.type === "REQUEST") {
      console.log("==== REQUEST ====");
    }
    if (e.data.type === "LOADED") {
      console.log("==== LOADED ====");
    }
    if (e.data.type === "PLAYER_ERROR") {
      console.log("==== PLAYER_ERROR ====");
      contentPlayBack();
    }
    if (e.data.type === "ERROR") {
      console.log("==== ERROR ====");
    }
    if (e.data.type === "BEGIN") {
      console.log("==== BEGIN ====");
    }
    if (e.data.type === "START") {
      console.log("==== START ====");
      if (!video.paused) {
        video.pause();
        wiiSdk.playAds();
        adsContainer.zIndex = 10;
      }
      renderSkipButton();
    }
    if (e.data.type === "GET_ADS") {
      console.log("==== GET_ADS ====");
      wiiSdk.setSource && wiiSdk.setSource();
    }
    if (e.data.type === "PAUSED") {
      console.log("==== PAUSED ====");
    }
    if (e.data.type === "RESUMED") {
      console.log("==== RESUMED ====");
    }
    if (e.data.type === "CLICK") {
      console.log("==== CLICK ====");
    }
    if (e.data.type === "IMPRESSION") {
      console.log("==== IMPRESSION ====");
    }
    if (e.data.type === "SKIPPED") {
      console.log("==== SKIPPED ====");
    }
    if (e.data.type === "END") {
      console.log("==== END ====");
      contentPlayBack();
    }
    if (e.data.type === "COMPLETE") {
      console.log("==== COMPLETE ====");
      contentPlayBack();
    }
    if (e.data.type === "All_ADS_COMPLETE") {
      console.log("==== All_ADS_COMPLETE ====");
      contentPlayBack();
    }
    if (e.data.type === "DESTROY") {
      console.log("==== DESTROY ====");
    }
    if (e.data.type === "FULLSCREEN") {
      console.log("==== FULLSCREEN ====");
    }
  });
  
  window.wiiSdk = new WI.InstreamSdk({
    env: WI.Environment.SANDBOX,
    tenantId: 14,
    deviceType: WI.DeviceType.TV,
    domId: "wiinvent_ads_container_id",
    channelId: "",
    streamId: "1509",
    adId: "1999",
    contentType: WI.ContentType.VIDEO,
    title: "noi dung 1",
    transId: "111",
    category: "1, 2",
    keyword: "1, 2",
    age: "20",
    gender: WI.Gender.MALE,
    uid20: "test",
    manufacturer: "",
    model: "",
    osName: "",
    osVersion: "",
    partnerSkipOffset: 2,
    vastLoadTimeout: 5,
    mediaLoadTimeout: 10,
    bufferingVideoTimeout: 10,
    alwaysCustomSkip: true,
    isAutoRequestFocus: false,
    bitrate: 1024,
    skipText: "Skip ads after {0} seconds",
    skippableText: "Skip ads",
    isUsePartnerSkipButton: true,
    segments: "1,2,3,11,22,33"
  })
  
  wiiSdk.start();
}


function renderSkipButton() {
  // Create the skip button element
  controls && visibableEle(controls, false);
  skipButton = document.createElement('button');
  
  skipButton.style.cssText = `
        background-color: #000000;
        color: white;
        border: none;
        cursor: not-allowed;
        display: flex;
        align-items: center;
        justify-content: center;
        position: absolute;
        bottom: 20px;
        right: 20px;
        visibility: hidden;
        border: 1px solid rgba(255,255,255,.5);
        font-size: 1.1rem;
        line-height: 1.6rem;
        font-family: "Inter",Tahoma,Geneva,Verdana,sans-serif;
        font-weight: 700;
        color: #fff;
        border-radius: .3rem;
        padding: .7rem;
        z-index: 100;
      `;
  adsContainer.appendChild(skipButton);
  // Determine countdown and type of ad (skippable or non-skippable)
  let countdown = 5;
  let type = 'skip';
  
  // Set button text based on ad type
  const text = {
    skip:
      `Skip in countdown seconds` +
      ' &nbsp ' +
      '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 320 512" font-size=\'14px\'><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><style>svg{fill:#ffffff}</style><path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416V96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241V96c0-17.7 14.3-32 32-32s32 14.3 32 32V416c0 17.7-14.3 32-32 32s-32-14.3-32-32V271l-11.5 9.6-192 160z"/></svg>',
    skipDone:
      'Skip ads ' +
      ' &nbsp ' +
      '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 320 512" font-size=\'14px\'><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><style>svg{fill:#ffffff}</style><path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416V96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241V96c0-17.7 14.3-32 32-32s32 14.3 32 32V416c0 17.7-14.3 32-32 32s-32-14.3-32-32V271l-11.5 9.6-192 160z"/></svg>',
    nonSkip:
      `Ad ends in countdown seconds` +
      ' &nbsp ' +
      '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 320 512" font-size=\'14px\'><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><style>svg{fill:#ffffff}</style><path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416V96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241V96c0-17.7 14.3-32 32-32s32 14.3 32 32V416c0 17.7-14.3 32-32 32s-32-14.3-32-32V271l-11.5 9.6-192 160z"/></svg>',
  };
  
  skipButton.addEventListener('mouseover', () => {
    skipButton.style.backgroundColor = 'rgba(0,0,0,.9)';
    skipButton.style.border = '1px solid rgb(255,255,255)';
  });
  
  skipButton.addEventListener('mouseout', () => {
    skipButton.style.backgroundColor = '#000000';
    skipButton.style.border = '1px solid rgba(255,255,255,.5)';
  });
  
  skipButton.addEventListener('focus', () => {
    skipButton.style.backgroundColor = 'rgba(0,0,0,.9)';
    skipButton.style.border = '1px solid rgb(255,255,255)';
  });
  
  skipButton.addEventListener('blur', () => {
    skipButton.style.boxShadow = 'none'; // Remove glow effect on blur
  });
  
  // Disable skip button initially if it's a skippable ad
  if (type === 'skip') {
    skipButton.disabled = true;
  }
  
  // Start countdown timer
  var timer = setInterval(() => {
    if (!skipButton) {
      clearInterval(timer);
      return;
    }
    visibableEle(skipButton, true);
    skipButton.innerHTML = text[type].replace('countdown', countdown);
    
    // Enable the skip button when the countdown finishes (if it's skippable)
    if (type === 'skip' && countdown <= 0) {
      skipButton.disabled = false;
      skipButton.innerHTML = text.skipDone;
    }
    if (type === 'nonSkip' && countdown <= 0) {
      visibableEle(skipButton, false);
      clearInterval(timer);
    }
    countdown -= 1;
  }, 1000);
  
  skipButton.addEventListener('click', () => {
    if (countdown <= 0) {
      wiiSdk.skip();
      contentPlayBack();
    }
  });
  window.addEventListener('keydown', () => {
    if (event.key === 'Enter' || event.keyCode === 13) {
      if (countdown <= 0) {
        wiiSdk.skip();
        contentPlayBack();
      }
    }
  })
}

function visibableEle(ele, visibable = true) {
  ele &&
  (ele.style.visibility = visibable ? 'visible' : 'hidden');
}

function displayEle(ele, display = true) {
  ele &&
  (ele.style.display = display ? 'block' : 'none');
}

function contentPlayBack() {
  // Resume video playback
  video.play();
  skipButton && adsContainer && adsContainer.removeChild(skipButton);
  controls && visibableEle(controls, true);
  displayEle(adsContainer, false);
}

var video = document.getElementById('video');
var progress = document.getElementById('progress');
var time = document.getElementById('time');

// Update progress bar and time
video.addEventListener('timeupdate', () => {
  const progressPercent = (video.currentTime / video.duration) * 100;
  progress.value = progressPercent;
  
  // Update time display
  const currentMinutes = Math.floor(video.currentTime / 60);
  const currentSeconds = Math.floor(video.currentTime % 60)
    .toString()
    .padStart(2, '0');
  const durationMinutes = Math.floor(video.duration / 60);
  const durationSeconds = Math.floor(video.duration % 60)
    .toString()
    .padStart(2, '0');
  time.textContent = `${currentMinutes}:${currentSeconds} / ${durationMinutes}:${durationSeconds}`;
});

// Seek video
progress.addEventListener('input', () => {
  const seekTime = (progress.value / 100) * video.duration;
  video.currentTime = seekTime;
});

var video = document.getElementById('video');
var progress = document.getElementById('progress');
var time = document.getElementById('time');


// Update progress bar and time
video.addEventListener('timeupdate', () => {
  const progressPercent = (video.currentTime / video.duration) * 100;
  progress.value = progressPercent;
  
  // Update time display
  const currentMinutes = Math.floor(video.currentTime / 60);
  const currentSeconds = Math.floor(video.currentTime % 60)
    .toString()
    .padStart(2, '0');
  const durationMinutes = Math.floor(video.duration / 60);
  const durationSeconds = Math.floor(video.duration % 60)
    .toString()
    .padStart(2, '0');
  time.textContent = `${currentMinutes}:${currentSeconds} / ${durationMinutes}:${durationSeconds}`;
});

// Seek video
progress.addEventListener('input', () => {
  const seekTime = (progress.value / 100) * video.duration;
  video.currentTime = seekTime;
});

```
2. Code Welcome Sample:

```javascript
var wiiSdk = [];
var skipButton = null;
wiiSdk = new WI.WelcomeSdk({
  env: WI.Environment.SANDBOX,
  tenantId: 14,
  contentType: "VOD",
  title: "noi dung 1",
  transId: "111",
  category: "1, 2",
  keyword: "1, 2",
  age: "20",
  gender: "MALE",
  uid20: "test",
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
  isUsePartnerSkipButton: true,
  segments: "1,2,3,11,22,33"
})


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
      "END",
      "All_ADS_COMPLETE",
    ].includes(e.data.type)
  ) {
    console.log("mmmm", e);
    
    if (e.data.type === "START") {
      renderSkipButton(document.getElementById('wii_Welcome'));
    }
    if (e.data.type === "COMPLETE" || e.data.type === "All_ADS_COMPLETE") {
      contentPlayBack();
    
    }
  }
});

function renderSkipButton(adsContainer) {
  // Create the skip button element
  skipButton = document.createElement('button');
  
  skipButton.style.cssText = `
      background-color: #000000;
      color: white;
      border: none;
      cursor: not-allowed;
      display: flex;
      align-items: center;
      justify-content: center;
      position: absolute;
      bottom: 20px;
      right: 20px;
      visibility: hidden;
      border: 1px solid rgba(255,255,255,.5);
      font-size: 1.1rem;
      line-height: 1.6rem;
      font-family: "Inter",Tahoma,Geneva,Verdana,sans-serif;
      font-weight: 700;
      color: #fff;
      border-radius: .3rem;
      padding: .7rem;
      z-index: 100;
    `;
  adsContainer.appendChild(skipButton);
  // Determine countdown and type of ad (skippable or non-skippable)
  let countdown = 5;
  let type = 'skip';
  
  // Set button text based on ad type
  const text = {
    skip:
      `Skip in countdown seconds` +
      ' &nbsp ' +
      '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 320 512" font-size=\'14px\'><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><style>svg{fill:#ffffff}</style><path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416V96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241V96c0-17.7 14.3-32 32-32s32 14.3 32 32V416c0 17.7-14.3 32-32 32s-32-14.3-32-32V271l-11.5 9.6-192 160z"/></svg>',
    skipDone:
      'Skip ads ' +
      ' &nbsp ' +
      '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 320 512" font-size=\'14px\'><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><style>svg{fill:#ffffff}</style><path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416V96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241V96c0-17.7 14.3-32 32-32s32 14.3 32 32V416c0 17.7-14.3 32-32 32s-32-14.3-32-32V271l-11.5 9.6-192 160z"/></svg>',
    nonSkip:
      `Ad ends in countdown seconds` +
      ' &nbsp ' +
      '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 320 512" font-size=\'14px\'><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><style>svg{fill:#ffffff}</style><path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416V96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241V96c0-17.7 14.3-32 32-32s32 14.3 32 32V416c0 17.7-14.3 32-32 32s-32-14.3-32-32V271l-11.5 9.6-192 160z"/></svg>',
  };
  
  skipButton.addEventListener('mouseover', () => {
    skipButton.style.backgroundColor = 'rgba(0,0,0,.9)';
    skipButton.style.border = '1px solid rgb(255,255,255)';
  });
  
  skipButton.addEventListener('mouseout', () => {
    skipButton.style.backgroundColor = '#000000';
    skipButton.style.border = '1px solid rgba(255,255,255,.5)';
  });
  
  skipButton.addEventListener('focus', () => {
    skipButton.style.backgroundColor = 'rgba(0,0,0,.9)';
    skipButton.style.border = '1px solid rgb(255,255,255)';
  });
  
  skipButton.addEventListener('blur', () => {
    skipButton.style.boxShadow = 'none'; // Remove glow effect on blur
  });
  
  // Disable skip button initially if it's a skippable ad
  if (type === 'skip') {
    skipButton.disabled = true;
  }
  
  // Start countdown timer
  var timer = setInterval(() => {
    if (!skipButton) {
      clearInterval(timer);
      return;
    }
    visibableEle(skipButton, true);
    skipButton.innerHTML = text[type].replace('countdown', countdown);
    
    // Enable the skip button when the countdown finishes (if it's skippable)
    if (type === 'skip' && countdown <= 0) {
      skipButton.disabled = false;
      skipButton.innerHTML = text.skipDone;
    }
    if (type === 'nonSkip' && countdown <= 0) {
      visibableEle(skipButton, false);
      clearInterval(timer);
    }
    countdown -= 1;
  }, 1000);
  
  skipButton.addEventListener('click', () => {
    if (countdown <= 0) {
      wiiSdk.skip();
      contentPlayBack();
    }
  });
  window.addEventListener('keydown', () => {
    if (event.key === 'Enter' || event.keyCode === 13) {
      if (countdown <= 0) {
        wiiSdk.skip();
        contentPlayBack();
      }
    }
  })
}

function visibableEle(ele, visibable = true) {
  ele &&
  (ele.style.visibility = visibable ? 'visible' : 'hidden');
}

function displayEle(ele, display = true) {
  ele &&
  (ele.style.display = display ? 'block' : 'none');
}

function contentPlayBack() {
  // Play content
  console.log('Play content');
  document.body.removeChild(document.getElementById('wii_Welcome'));
}

var firstTime = false;
window.addEventListener('keydown', (e) => {
  console.log("mmmm", e);
  if (firstTime) return;
  firstTime = true;
  wiiSdk.start();
})

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
| segments              | Danh sách segment id của user                                                     |   string |

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
| EventType | GET_ADS          | Fired when the ad link gotten                 |
| EventType | LOADED           | Fired when the ad loaded                      |
| EventType | START            | Fired when the ad starts playing              |
| EventType | IMPRESSION       | Fired when the impression URL has been pinged |
| EventType | COMPLETE         | Fired when the ad completes playing           |
| EventType | SKIPPED          | Fired when the ad is skipped by the user      |
| EventType | BUFFERING        | Fired when the ad is buffering                |
| EventType | ERROR            | Fired when the ad has an error                |
| EventType | DESTROY          | Fires when the ad destroyed                   |
| EventType | ALL_ADS_COMPLETE | Fires when no more ad will show up            |
