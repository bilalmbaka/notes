# CHAPTER 1

# RUNNING FUNCTIONS IN BACKGROUND/FOREGROUND

### sources

- [Foregrond service types](https://developer.android.com/develop/background-work/services/fgs/service-types)
- [Forground services documentation](https://developer.android.com/develop/background-work/services/fgs/declare)

## Android concepts

## Foreground Services

### Forground service types

Beginning with Android 14 (API level 34), you must declare an appropriate service type for each foreground service. That means you must declare the service type in your app manifest, and also request the appropriate foreground service permission for that type (in addition to requesting the FOREGROUND_SERVICE permission).

[Foregrond service types](https://developer.android.com/develop/background-work/services/fgs/service-types)

### Declare foreground services in the app manifest

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" ...>
  <application ...>

    <service
        android:name=".MyMediaPlaybackService"
        android:foregroundServiceType="mediaPlayback"  #camera|microphone
        android:exported="false">
    </service>
  </application>
</manifest>

```

### Request the foreground service permissions

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" ...>

    <uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE_CAMERA"/>

    <application ...>
        ...
    </application>
</manifest>
```

# CHAPTER 2
# FLAVORING

## Ios

If you want to create Flutter flavors for an iOS app, you'll need to do so in Xcode. Xcode does not have a concept called "flavor". Instead, you'll need to set up something called a scheme and attach custom configurations to it.

``` bash
open ios/Runner.xcworkspace
```


# CHAPTER 3
# NOTIFICATIONS

## Ios setup

