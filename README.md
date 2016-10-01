# slack-api-android [![Release](https://jitpack.io/v/pschroen/slack-api-android.svg)](https://jitpack.io/#pschroen/slack-api-android) [![Build Status](https://travis-ci.org/pschroen/slack-api-android.svg)](https://travis-ci.org/pschroen/slack-api-android)

Port of [allbegray](https://github.com/allbegray)'s [slack-api](https://github.com/allbegray/slack-api).

https://jitpack.io/#pschroen/slack-api-android

Add it to your build.gradle with:

```gradle
allprojects {
    repositories {
        maven { url "https://jitpack.io" }
    }
}
```
and:

```gradle
dependencies {
    compile 'com.github.pschroen:slack-api-android:v1.3.0-RELEASE'
}
```

## Differences

- Compiled with Java 7.
- Commons Logging replaced with Android Logging.
- `org.apache.http` classes replaced with HttpClient for Android [maintained by Marek Sebera](https://hc.apache.org/httpcomponents-client-4.5.x/android-port.html).
- `org.asynchttpclient` classes replaced with [OkHttp](http://square.github.io/okhttp/) Web Sockets and [Socket.IO](https://github.com/socketio)'s [WebSocket.java](https://github.com/socketio/engine.io-client-java/blob/master/src/main/java/io/socket/engineio/client/transports/WebSocket.java) as a reference.
