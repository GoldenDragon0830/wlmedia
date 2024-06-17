## &#x1F680;wlmedia&#x1F680;
#### Android audio and video playback SDK, you can realize the audio and video playback function with just a few lines of code~
#### Easy to use, rich in functions, supports smart devices such as mobile phones, car systems, TV boxes, watches, etc.
#### New architecture to increase stability
#### HarmonyOS Coming Soon
- If there are partners with `HarmonyOS NEXT` development qualifications who want to cooperate, please contact: [ywl5320@163.com](mailto:ywl5320@163.com)
#### iOS Coming Soon
## 1 Functions & Features
- [x] Supported system: Android 4.4+ (api 19+)
- [x] Supported architectures: armeabi-v7a, arm64-v8a, x86, x86_64
- [x] Basic information acquisition (audio: sampling rate, number of channels, duration, etc., video: width, height, fps, duration, etc.)
- [x] Support file, http, https, rtmp, rtp, rtsp, byte[], etc.
- [x] Support AV1 decoding
- [x] Optional audio, video, audio and video playback modes
- [x] Soft solution and hard solution settings
- [x] Seamlessly switch surfaces (surfaceview and textureview can also be customized)
- [x] Support multi-instance playback
- [x] Support after playback is completed (EOF), seek again and continue playback
- [x] Support free switching of media
- [x] Support FFmpeg parameter setting
- [x] Support byte[] data decoding
- [x] Support audio and video speed and pitch changes
- [x] Support transparent video playback (can achieve good visual effects)
- [x] Subtitle selection
- [x] Built-in loop playback
- [x] Link timeout settings
- [x] Cache size setting (time dimension)
- [x] Encrypted audio and video playback
- [x] Audio track selection
- [x] Audio specified sample rate settings
- [x] Audio specified channel playback
- [x] Video screenshot
- [x] Obtain the first frame picture of the video or the picture at the specified time
- [x] Video arbitrary ratio setting
- [x] Video rotation angle setting (0,90,180,270)
- [x] Video mirroring mode settings
- [x] Video background color setting (default black)
- [x] Video supports multiple surface rendering at the same time (such as: KTV size screen)
- [x] Support Unity playback (customized docking required)
## 2 Integrated use
### 2.1 gradle [![](https://jitpack.io/v/wanliyang1990/wlmedia.svg)](https://jitpack.io/#wanliyang1990/wlmedia)
```gradle
allprojects {
 repositories {
 ...
 maven { url 'https://jitpack.io' }
 }
}

dependencies {
 implementation 'com.github.wanliyang1990:wlmedia:3.0.0'
}
 ```
 ### 2.2 Common permissions
 ```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.INTERNET"/>
 ```

 ### 2.3 Configure NDK compilation platform
 ```gradle
 defaultConfig {
 ...
 ndk {
 abiFilter("armeabi-v7a")
 abiFilter("arm64-v8a")
 abiFilter("x86")
 abiFilter("x86_64")
 }
...
}
 ```
 ### 2.4 Setting up Surface
 ```xml
 <-- WlSurfaceView is used for general playback -->
 <com.ywl5320.wlmedia.widget.WlSurfaceView
 android:id="@+id/wlsurfaceview"
 android:layout_width="match_parent"
 android:layout_height="match_parent" />

 <-- WlTextureView needs to be used for transparency, movement, rotation, etc. -->
 <com.ywl5320.wlmedia.widget.WlTextureView
 android:id="@+id/wltextureview"
 android:layout_width="match_parent"
 android:layout_height="match_parent" />
 ```
 ### 2.5 Basic calling code (see Demo for more functions)
 ```java
 // 1. Create player
 WlPlayer wlPlayer = new WlPlayer();
 wlPlayer.setOnMediaInfoListener(new WlOnMediaInfoListener() {
 @Override
 public void onPrepared() {
 // Callback after asynchronous preparation, call wwlplayer.start() here to start playback
 wlPlayer.start();
 }

 @Override
 public void onTimeInfo(double currentTime, double bufferTime) {
 // Time progress callback
 }

 @Override
 public void onComplete(WlCompleteType wlCompleteType, String msg) {
 // Playback completion callback, distinguish corresponding types according to WlCompleteType
 if (wlCompleteType == WlCompleteType.WL_COMPLETE_EOF) {
 // Normal playback completed
 } else if (wlCompleteType == WlCompleteType.WL_COMPLETE_ERROR) {
 // Playback error, reason: msg field
 } else if (wlCompleteType == WlCompleteType.WL_COMPLETE_HANDLE) {
 // Actively calling wlPlayer.stop() will call back this type
 } else if (wlCompleteType == WlCompleteType.WL_COMPLETE_NEXT) {
 // While playing, if a new data source is switched, this type will be called back.
 } else if (wlCompleteType == WlCompleteType.WL_COMPLETE_TIMEOUT) {
 // Playback times out, this interface will be called back
 } else if (wlCompleteType == WlCompleteType.WL_COMPLETE_RELEASE) {
 // Calling wlPlayer.release() during playback will call back this interface
 }
 }

 @Override
 public void onLoad(WlLoadStatus wlLoadStatus, int progress, long speed) {
 //Loading status callback
 if (wlLoadStatus == WlLoadStatus.WL_LOADING_STATUS_START) {
 // Start loading
 } else if (wlLoadStatus == WlLoadStatus.WL_LOADING_STATUS_PROGRESS) {
 //Loading progress
 } else if (wlLoadStatus == WlLoadStatus.WL_LOADING_STATUS_FINISH) {
 // Loading completed
 }
 }

 @Override
 public void onTakePicture(Bitmap bitmap) {
 // Screenshot callback
 }

 @Override
 public void onAutoPlay() {
 // If wlplayer.setAutoPlay(true) is set, this interface will be called back when the asynchronous is ready.
 }

 @Override
 public void onFirstFrameRendered() {
 //First frame rendering callback
 }

 @Override
 public void onSeekFinish() {
 // seek completion callback
 }

 @Override
 public byte[] decryptBuffer(byte[] encryptBuffer, long position) {
 // (Sub-thread) Encrypted video data will be returned through this interface, and then returned to the player after decryption
 return encryptBuffer;
 }

 @Override
 public byte[] readBuffer(int read_size) {
 // (Sub-thread) byte[] type buffer, provides data to the player through this interface
 return null;
 }

 @Override
 public void onOutRenderInfo(WlOutRenderBean outRenderBean) {
 // For external rendering to obtain initialization information, such as unity playback.
 }
});

 // 2. Get WlSurfaceView and bind the player
 WlSurfaceView wlSurfaceView = findViewById(R.id.wlsurfaceview);
 wlSurfaceView.setWlPlayer(wlPlayer);

 // 3. Set up data source asynchronous preparation
 wlPlayer.setSource(url);
 wlPlayer.prepare();
 ```

## Three detailed API
- [1. WlPlayer (audio and video playback SDK)](doc/wlplayer_api.md)
- [2. WlMediaUtil (audio and video tool SDK)](doc/wlmediautil_api.md)
- 3.HarmonyOS (coming soon)
- 4.iOS (coming soon)
## Four Effect Display

| Regular playback | Transparent video | Multi-Surface rendering | Multi-instance playback |
| :----: | :----: | :----: | ------ |
| <img src="doc/imgs/normal.gif" alt="Description" width="240" height="520"> | <img src="doc/imgs/alphavideo.gif" alt="Description" width ="240"
