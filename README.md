# slack-api-android [![Release](https://jitpack.io/v/pschroen/slack-api-android.svg)](https://jitpack.io/#pschroen/slack-api-android) [![Build Status](https://travis-ci.org/pschroen/slack-api-android.svg)](https://travis-ci.org/pschroen/slack-api-android)

Port of [allbegray](https://github.com/allbegray)'s [slack-api](https://github.com/allbegray/slack-api). Used in the production app [Amp](https://ufo.ai/amp/).

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
    compile 'com.github.pschroen:slack-api-android:-android-19-v1.3.0-RELEASE-gb78e900-11'
}
```

## Differences

- Compiled with Java 7.
- Commons Logging replaced with Android Logging.
- `org.apache.http` classes replaced with HttpClient for Android [maintained by Marek Sebera](https://hc.apache.org/httpcomponents-client-4.5.x/android-port.html).
- `org.asynchttpclient` classes replaced with [OkHttp](http://square.github.io/okhttp/) Web Sockets and [Socket.IO](https://github.com/socketio)'s [WebSocket.java](https://github.com/socketio/engine.io-client-java/blob/master/src/main/java/io/socket/engineio/client/transports/WebSocket.java) as a reference.

## Usage

```java
final String slackToken = mSharedPref.getString("slack_token", "");

mWebApiClient = SlackClientFactory.createWebApiClient(slackToken);
String webSocketUrl = mWebApiClient.startRealTimeMessagingApi().findPath("url").asText();
mRtmClient = new SlackRealTimeMessagingClient(webSocketUrl);

mRtmClient.addListener(Event.HELLO, new EventListener() {

    @Override
    public void onMessage(JsonNode message) {
        Authentication authentication = mWebApiClient.auth();
        mBotId = authentication.getUser_id();

        System.out.println("User id: " + mBotId);
        System.out.println("Team name: " + authentication.getTeam());
        System.out.println("User name: " + authentication.getUser());
    }
});

mRtmClient.addListener(Event.MESSAGE, new EventListener() {

    @Override
    public void onMessage(JsonNode message) {
        String channelId = message.findPath("channel").asText();
        String userId = message.findPath("user").asText();
        String text = message.findPath("text").asText();

        if (userId != null && !userId.equals(mBotId)) {
            Channel channel;
            try {
                channel = mWebApiClient.getChannelInfo(channelId);
            } catch (SlackResponseErrorException e) {
                channel = null;
            }
            User user = mWebApiClient.getUserInfo(userId);
            String userName = user.getName();

            System.out.println("Channel id: " + channelId);
            System.out.println("Channel name: " + (channel != null ? "#" + channel.getName() : "DM"));
            System.out.println("User id: " + userId);
            System.out.println("User name: " + userName);
            System.out.println("Text: " + text);

            // Copy cat
            mWebApiClient.meMessage(channelId, userName + ": " + text);
        }
    }
});

mRtmClient.connect();
```

If you need to catch the close and failure events you can add listeners.

```java
mRtmClient.addCloseListener(new CloseListener() {

    @Override
    public void onClose() {
        System.out.println("Connection closed");
    }
});

mRtmClient.addFailureListener(new FailureListener() {

    @Override
    public void onFailure(Throwable t) {
        Exception e = (Exception) t;

        System.out.println("Failure message: " + e.getMessage());
    }
});
```

## ProGuard

```
-dontwarn allbegray.slack.**
-keep class allbegray.slack.** {*;}
-dontwarn com.fasterxml.**
-keep class com.fasterxml.** {*;}
-dontwarn okio.**
-keep class okio.** {*;}
```
