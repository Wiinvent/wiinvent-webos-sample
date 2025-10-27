SDK:

````javascript
<script src="https://wiinvent.tv/sdk/tv/wii-sdk-3.0.3.js"></script>
````

1. Code Instream Sample:

```javascript
// --- Player Elements ---
const video = document.getElementById('content-video');
const playPauseBtn = document.getElementById('play-pause');
const adsContainer = document.getElementById('wiinvent_ads_container_id');
const controls = document.querySelector('.controls');

let wiiSdk = null;
let adsInitialized = false;
let skipButton = null; // Keep a reference to the skip button
let isAdPlaying = false;
// --- Main Player Logic ---
playPauseBtn.addEventListener('click', () => {
  if (video.paused || video.ended) video.play();
  else video.pause();
});

video.addEventListener('play', () => {
  playPauseBtn.textContent = 'Pause';
  if (!adsInitialized) {
    handleAds();
    adsInitialized = true;
  }
});

video.addEventListener('pause', () => {
  playPauseBtn.textContent = 'Play';
});

// --- SDK ENUMS SETUP (FIXED) ---

//   // Create a single global object to hold all SDK enums.
//   const WI = {};

//   WI.EventType = {
//     'REQUEST': 'REQUEST', 'START': 'START', 'IMPRESSION': 'IMPRESSION',
//     "CLICK": "CLICK", 'COMPLETE': 'COMPLETE', "SKIPPED": "SKIPPED",
//     'ERROR': 'ERROR', /* ...and so on */
//   };

//   WI.Environment = {
//     'SANDBOX': 'SANDBOX', 'PRODUCTION': "PRODUCTION",
//     'VIETTEL_PRODUCTION': "VIETTEL_PRODUCTION",
//   };

//   WI.DeviceType = { 'PHONE': 'PHONE', 'TV': 'TV', 'WEB': 'WEB' };

//   WI.ContentType = {
//     'VOD': "VOD", "LIVE_STREAM": "LIVE_STREAM", "VIDEO": "VIDEO",
//   };

//   WI.Gender = { 'MALE': 'MALE', 'FEMALE': 'FEMALE', 'NONE': 'NONE' };

// --- SDK Initialization ---
function handleAds() {
  const sdkConfig = {
    domId: "wiinvent_ads_container_id",
    liveTv: false,
    env: WI.Environment.SANDBOX,
    tenantId: 14,
    deviceType: WI.DeviceType.TV,
    channelId: "115376",
    streamId: "23203", // 23173, 23203
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
  };
  // STEP 1: init Wii SDK
  window.wiiSdk = new WI.InstreamSdk(sdkConfig);
  // STEP 2: start SDK
  window.wiiSdk.start();
  // Optional STEP 2.1: wiiSdk.setDuration(duration)
  window.wiiSdk.setDuration(video.duration);
  
  // LOOP: Update currentTime content
  const timer = setInterval(() => {
    if (!window.wiiSdk || video.paused || video.ended) {
      return;
    }
    // console.log('[CLIENT] Updating SDK with current time:', video.currentTime);
    window.wiiSdk.updateTime(video.currentTime, video.duration);
  }, 200);
}

// --- Event Listening & UI Control ---
window.addEventListener("message", function (event) {
  if (!event.data || !event.data.type) return;
  
  const eventType = event.data.type;
  console.log(`[CLIENT] Received ad event: ${eventType}`, event.data);
  
  // Ad break end events
  const adBreakEndEvents = ['PAUSE_CONTENT', 'COMPLETE', 'SKIPPED', 'ERROR', 'ALL_ADS_COMPLETED', 'END'];
  
  // STEP 6: Event: PAUSE_CONTENT
  if (eventType === 'PAUSE_CONTENT') {
    // Ad has loaded, we can pause content if not already paused
    // STEP 7: Pause content video
    if (!video.paused) video.pause();
    console.log('[CLIENT] Ad break starting, content paused.', window.wiiSdk);
    // STEP 8: Callbacl playAd to trigger ad break
    window.wiiSdk.playAd(); // Trigger the ad break to play
  }
  
  // STEP 14: Event: START
  if (eventType === 'START') {
    // Hide content controls and render the custom skip button
    controls.classList.add('hidden');
    // STEP 15: Call renderSkipButton function
    renderSkipButton(event.data); // Pass ad data to the function
    isAdPlaying = true;
  }
  if (eventType === 'ALL_ADS_COMPLETED') {
    // Show content controls and destroy the custom skip button
    console.log('[CLIENT] Ad break ended, resuming content.', isAdPlaying, video.paused);
    if (isAdPlaying && video.paused) {
      video.play();
      controls.classList.remove('hidden');
      destroySkipButton();
      isAdPlaying = false;
    }
  }
});

// --- Custom Skip Button Logic ---

/**
 * Renders the custom skip button based on ad data.
 * @param {object} adData - Data from the SDK's START event.
 */
function renderSkipButton(adData) {
  // Prevent creating multiple buttons
  if (skipButton) destroySkipButton();
  
  skipButton = document.createElement('button');
  skipButton.style.cssText = `
        background-color: #000000; color: white; border: none; cursor: not-allowed; display: flex;
        align-items: center; justify-content: center; position: absolute; bottom: 20px; right: 20px;
        visibility: hidden; border: 1px solid rgba(255,255,255,.5); font-size: 1.1rem; line-height: 1.6rem;
        font-family: "Inter",Tahoma,Geneva,Verdana,sans-serif; font-weight: 700; border-radius: .3rem;
        padding: .7rem; z-index: 1000;
      `;
  adsContainer.appendChild(skipButton);
  
  // Use dynamic data from the event, with defaults
  const isSkippable = adData.skippable !== false; // Skippable by default
  let countdown = adData.skipOffset || 5;
  const type = isSkippable ? 'skip' : 'nonSkip';
  
  const text = {
    skip: `Bỏ qua sau countdown`,
    skipDone: 'Bỏ qua quảng cáo',
    nonSkip: `Quảng cáo kết thúc sau countdown`
  };
  
  if (isSkippable) {
    skipButton.disabled = true;
  }
  
  const timer = setInterval(() => {
    if (!skipButton) {
      clearInterval(timer);
      return;
    }
    skipButton.style.visibility = 'visible';
    skipButton.innerHTML = text[type].replace('countdown', countdown);
    
    if (isSkippable && countdown <= 0) {
      skipButton.disabled = false;
      skipButton.style.cursor = 'pointer';
      skipButton.innerHTML = text.skipDone;
      clearInterval(timer); // Stop countdown once skippable
    }
    if (!isSkippable && countdown <= 0) {
      destroySkipButton(); // Auto-remove non-skippable button when countdown ends
      clearInterval(timer);
    }
    countdown -= 1;
  }, 1000);
  
  // STEP 16: Handle skip button click
  skipButton.addEventListener('click', () => {
    if (!skipButton.disabled) {
      // IMPORTANT: The SDK must have a public .skip() method.
      // STEP 17: Call SDK skip method on button click
      window.wiiSdk.skip();
      // The SDK will fire a SKIPPED event, which the main listener will catch to clean up UI.
    }
  });
}

function destroySkipButton() {
  if (skipButton) {
    skipButton.remove();
    skipButton = null;
  }
}

// --- Standard Player Progress Bar (MODIFIED) ---
video.addEventListener('timeupdate', () => {
  // MODIFIED: Check the flag. If an ad is playing, do not update the progress bar.
  if (isAdPlaying || isNaN(video.duration)) {
    return;
  }
  
  const progressPercent = (video.currentTime / video.duration) * 100;
  progress.value = progressPercent;
  
  const formatTime = (seconds) => {
    const min = Math.floor(seconds / 60);
    const sec = Math.floor(seconds % 60).toString().padStart(2, '0');
    return `${min}:${sec}`;
  };
  time.textContent = `${formatTime(video.currentTime)} / ${formatTime(video.duration)}`;
});

progress.addEventListener('input', () => {
  if (isAdPlaying || isNaN(video.duration)) return;
  const seekTime = (progress.value / 100) * video.duration;
  video.currentTime = seekTime;
});

```
1.1. Diagram for instream

![Instream Diagram](/diagram.jpg)

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
