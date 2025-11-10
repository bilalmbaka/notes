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
# CODE PUSH

## Shorebird

### sources

- [Shorebird documentation](https://docs.shorebird.dev/code-push/)


``` bash
> shorebird login
```

``` bash
> shorebird init --display-name "My App"
```


### Application
An application is what is created by running flutter create [app_name] and corresponds to a listing in the App Store or Play Store.

Each application has a unique app_id that is assigned when you run shorebird init. You can find your application’s id in the shorebird.yaml file at the root of your project.


### Release
A release is a specific version of an application, identified by a version and build number (e.g. 1.0.0+1). Although code push works for apps distributed outside of the App Store and Play Store, a release most often corresponds with a specific version of your app that is published to the App Store or Play Store.

A release can have zero or more patches applied to it.
Releases are created by running shorebird release [platform], where platform is android, aar, ios, or macos.

### Patch
A patch is a change to a specific release, applied as an over-the-air update. For example, a patch could be a bug fix or a new feature. Multiple patches can be published for a given release, although only one patch can be active at a time. Patches are identified by their associated release version and a patch number, which is an auto-incrementing integer.

When your application starts, it checks for available patches and applies the latest one. This patch will be visible the next time your application launches.

Patches are created by running shorebird patch [platform], where platform is android, aar, ios, or macos.

### Artifact
An artifact is the output of a build or patch operation. For example:

shorebird release android generates and uploads several architecture-specific libapp.so files and an Android archive (.aab) file. These are release artifacts.
shorebird patch android generates and uploads diff files that capture differences between your Dart code at patch time and the code in the associated release. These are patch artifacts.


## Create a Release

``` bash
> shorebird release android
> shorebird release android --target ./lib/main_development.dart --flavor development
> shorebird release android --flutter-version 3.19.0
> shorebird release android --artifact apk
```


**_shorebird release wraps flutter build and can take any argument flutter build can. To pass arguments to the underlying flutter build you need to put flutter build arguments after a -- separator. For example: shorebird release android -- --dart-define="foo=bar" will define the "foo" environment variable inside Dart as you might have done with flutter build directly. In Powershell the -- separator must be quoted: '--'._**


### Previewing a release

``` bash
> shorebird preview
```

## Create a Patch

``` bash
> shorebird patch android
```

This will do several things:

* Builds the artifacts for the update.
* Downloads the corresponding release artifacts.
* Generates a patch using the diff between the release and the current changes.
* Uploads the patch artifacts to the Shorebird backend
* Promotes the patch to the stable channel.

``` bash
> shorebird patch android --release-version latest
> shorebird patch android --release-version 0.1.0+1
```

you can also pass in flutter commands same as shorebird release.

## Roll back a Patch

When a patch is rolled back, it is remotely uninstalled from end users’ devices and replaced by either the previous patch or the base release if no previous patch is available.


Shorebird includes safety features to prevent needing to issue the manual rollback of a patch.

shorebird patch also generates a hash of your patch file which is stored separately from the patch and checked every time your patch is installed, or booted from on the user’s device.

Customers are also encouraged to use Shorebird’s patch-signing feature to cryptographically sign patch files. This uses the same cryptographic algorithms used in the Google and Apple app stores. When enabled, your public key is included in your app bundle and a patch’s signature is checked on every install and boot to ensure your app can never accept a patch file not signed by your secret key.

Finally Shorebird’s updater includes automatic on-device rollback. If for any reason a patch’s hash or signature does not match, the patch is discarded. If for any reason a patch fails to load into the Dart runtime, a patch will be marked “bad” on that device, and the app will be automatically rolled back to the latest installed patch.

The previous patch file is always kept on disk until at least one successful boot is made of the next patch, after which a patch file is automatically cleaned up from the user’s device to minimize disk usage.




### Uninstalling Shorebird

Building with shorebird release will include Shorebird code push in your app. Building with flutter build --release will not include Shorebird in your app. At any time you can simply drop back to flutter build and things will work as they did before.

You can remove shorebird from your path by removing it from your .bashrc or .zshrc and deleting the .shorebird directory located in ~/.shorebird.




# CHAPTER 4
# TESTING

## Widget testing 

### sources
[Flutter doc widget test](https://docs.flutter.dev/cookbook/testing/widget/introduction)

After the initial call to pumpWidget(), the WidgetTester provides additional ways to rebuild the same widget. This is useful if you're working with a StatefulWidget or animations.

* tester.pump(Duration duration)
* tester.pumpAndSettle()


## Testing with when using riverpod