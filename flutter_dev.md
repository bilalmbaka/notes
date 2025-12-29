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

```bash
open ios/Runner.xcworkspace
```

# CHAPTER 3

# CODE PUSH

## Shorebird

### sources

- [Shorebird documentation](https://docs.shorebird.dev/code-push/)

```bash
> shorebird login
```

```bash
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

```bash
> shorebird release android
> shorebird release android --target ./lib/main_development.dart --flavor development
> shorebird release android --flutter-version 3.19.0
> shorebird release android --artifact apk
```

**_shorebird release wraps flutter build and can take any argument flutter build can. To pass arguments to the underlying flutter build you need to put flutter build arguments after a -- separator. For example: shorebird release android -- --dart-define="foo=bar" will define the "foo" environment variable inside Dart as you might have done with flutter build directly. In Powershell the -- separator must be quoted: '--'._**

### Previewing a release

```bash
> shorebird preview
```

## Create a Patch

```bash
> shorebird patch android
```

This will do several things:

- Builds the artifacts for the update.
- Downloads the corresponding release artifacts.
- Generates a patch using the diff between the release and the current changes.
- Uploads the patch artifacts to the Shorebird backend
- Promotes the patch to the stable channel.

```bash
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

- tester.pump(Duration duration)
- tester.pumpAndSettle()

## Testing with when using riverpod

Firt you mock repo classes and service classes, then override the different
riverpod classes.

```dart
class TestHelpers {
  static Widget buildRoute({
    required Route<dynamic>? Function(RouteSettings) routes,
    List<Override> overrides = const [],
  }) {
    return ProviderScope(
      overrides: overrides,
      child: ScreenUtilInit(
        designSize: const Size(430, 932),
        minTextAdapt: true,
        splitScreenMode: true,
        child: MaterialApp(
          restorationScopeId: AppConstants.rootRestorationId,
          title: AppStrings.appName,
          navigatorKey: navigatorkey,
          onGenerateRoute: routes,
          navigatorObservers: [MyRouteObserver()],
        ),
      ),
    );
  }
}
```

```dart
    await tester.pumpWidget(
      TestHelpers.buildRoute(
        routes: LoginRoute.generateRoute,
        overrides: [
          loginViewModel.overrideWith(() {
            return LoginViewModel(
              authUseCase: AuthUsecase(
                profileRepository: fakeProfileRepository,
                authRepository: mockAuthRepository,
              ),
            );
          }),
          saveLoginViewModel.overrideWith(() {
            return SavedLoginViewModel(
              authUseCase: AuthUsecase(
                profileRepository: fakeProfileRepository,
                authRepository: mockAuthRepository,
              ),
            );
          }),
        ],
      ),
    );
```

### Scolling in widget tests

```dart
    //Scroll down.
    await tester.dragUntilVisible(
      find.text(AppStrings.dontHaveAnAccount),
      find.byKey(
        AppConstants.loginScreenKey,
      ),
      const Offset(0, 10),
    );
    await tester.pumpAndSettle();
```


## Integration testing

It is pretty much the same as widget testing you just add 

```dart
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();
````

to the start of main function.




# CHAPTER 5

# CI/CD

## Codemagic

sources
[Signing apps](https://docs.codemagic.io/yaml-code-signing/signing-android/)

### Signing

### Android apps

```bash
> keytool -genkey -v -keystore codemagic.keystore -storetype JKS \
        -keyalg RSA -keysize 2048 -validity 10000 -alias codemagic
```

**Uploading a keystore**

- Open your Codemagic Team settings, and go to codemagic.yaml settings > Code signing identities.
- Open Android keystores tab.
- Upload the keystore file by clicking on Choose a file or by dragging it into the indicated frame.
- Enter the Keystore password, Key alias and Key password values as indicated.
- Enter the keystore Reference name. This is a unique name used to reference the file in codemagic.yaml
- Click the Add keystore button to add the keystore.

**Referencing keystores in codemagic.yaml**<br>

**Fetching a single keystore file**

```yaml
workflows:
  android-workflow:
    name: Android Workflow

    # ....

    environment:
      android_signing:
        - keystore_reference
```

Default environment variables are assigned by Codemagic for the values on the build machine:

- Keystore path: CM_KEYSTORE_PATH
- Keystore password: CM_KEYSTORE_PASSWORD
- Key alias: CM_KEY_ALIAS
- Key alias password: CM_KEY_PASSWORD

_Fetching multiple keystore files_

```yaml
environment:
  android_signing:
    - keystore: keystore_reference_1

      keystore_environment_variable: THIS_KEYSTORE_PATH_ON_DISK_1

      keystore_password_environment_variable: THIS_KEYSTORE_PASSWORD_1

      key_alias_environment_variable: THIS_KEY_ALIAS_1

      key_password_environment_variable: THIS_KEY_PASSWORD_1

    - keystore: keystore_reference_2

      keystore_environment_variable: THIS_KEYSTORE_PATH_ON_DISK_2

      keystore_password_environment_variable: THIS_KEYSTORE_PASSWORD_2

      key_alias_environment_variable: THIS_KEY_ALIAS_2

      key_password_environment_variable: THIS_KEY_PASSWORD_2
```

**Signing Android apps using Gradle**

These will use the default keys.

```gradle
...
  android {

      ...

      defaultConfig { ... }

      signingConfigs {

          release {

              if (System.getenv()["CI"]) { // CI=true is exported by Codemagic

                  storeFile file(System.getenv()["CM_KEYSTORE_PATH"])

                  storePassword System.getenv()["CM_KEYSTORE_PASSWORD"]

                  keyAlias System.getenv()["CM_KEY_ALIAS"]

                  keyPassword System.getenv()["CM_KEY_PASSWORD"]

              } else {

                  keyAlias keystoreProperties['keyAlias']

                  keyPassword keystoreProperties['keyPassword']

                  storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null

                  storePassword keystoreProperties['storePassword']

              }

          }

      }

      buildTypes {

          release {

              ...

              signingConfig signingConfigs.release

          }

      }

  }
  ...
```

to use userdefined keys, after modifiying the build.gradle add this scirpt
to codemagic.yaml

```yaml
scripts:
  # ...

  - name: Set up key.properties

    script: |

      cat >> "$CM_BUILD_DIR/project_directory/android/key.properties" <<EOF

      storePassword=$CM_KEYSTORE_PASSWORD

      keyPassword=$CM_KEY_PASSWORD

      keyAlias=$CM_KEY_ALIAS

      storeFile=$CM_KEYSTORE_PATH

      EOF
```

### IOS APPS

<br>
<br>

### PUBLISHING

### ANDROID

<br><br>




<br>
<br>
<br>

## Using github actions and fastlane.

__sources__

[Fastlane on android](https://docs.fastlane.tools/getting-started/android/setup/)

1. install fastlane
```bash
gem install bundler
```

Fix ruby version Error.
```bash
brew install rbenv ruby-build
rbenv install 3.2.2
rbenv global 3.2.2

echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc

source ~/.zshrc

rbenv rehash

rbenv global 3.2.2
```

Every time you run fastlane, use bundle exec fastlane [lane]
On your CI, add bundle install as your first build step
To update fastlane, just run bundle update fastlane

__Navigate to your iOS or Android app and run__

**fastlane init**

fastlane will automatically detect your project, and ask for any missing information.

### Android setup

run fastlane init in android folder, it should generate two files
AppFile and FastFile

The most interesting file is fastlane/Fastfile, which contains all the information that is needed to distribute your app.



i. Firebase app distribution

```bash
> openssl base64 -e -in upload-keystore.jks -out ~/Downloads/key.txt
```



# Chapter 6

# PUBLISHING APPS

### Google play

```bash
keytool -genkey -v -keystore /desired-destination -keyalg RSA -keysize 2048 -validity 1000 -alias upload
```

In android/key.properties

```
storeFile=path
storePassword=password
keyAlias=alias
keyPassword=password
```

In android/app/build.gradle

```gradle
def keyproperties = new Properties()
def keyPropertiesFile = rootProject.file("key.properties")
if (keyPropertiesFile.exists()) {
    keyPropertiesFile.withReader('UTF-8') { reader ->
        keyproperties.load(reader)
    }
}

android {
  ...
     signingConfigs {
        release {
            if (keyPropertiesFile.exists()) {
                keyAlias keyproperties["keyAlias"]
                keyPassword keyproperties["keyPassword"]
                storeFile file(keyproperties["storeFile"])
                storePassword keyproperties["storePassword"]
            }
        }
    }

    buildTypes {
        release {
            signingConfig signingConfigs.release
        }

        debug {
            signingConfig signingConfigs.debug
        }
    }
    ...
}

```

or if you are using build.gradle.kts

```
val keystoreProperties = Properties()
val keystorePropertiesFile = rootProject.file("key.properties")
if (keystorePropertiesFile.exists()) {
    keystoreProperties.load(FileInputStream(keystorePropertiesFile))
}


android {
  ...
    signingConfigs {
        create("release") {
            keyAlias = keystoreProperties["keyAlias"] as String
            keyPassword = keystoreProperties["keyPassword"] as String
            storeFile = keystoreProperties["storeFile"]?.let { file(it) }
            storePassword = keystoreProperties["storePassword"] as String
        }
    }

    buildTypes {
        release {
            signingConfig = signingConfigs.getByName("release")
        }

        debug {
            signingConfigRelease = signingConfig.getByName("debug")
        }
    }
    ...


}
```

## Appstore

```bash
flutter build ipa
```

### Upload the app bundle to App Store Connect

__Method A__

1. open build/ios/archive/MyApp.xcarchive in Xcode.
2. Click the Validate App button. If any issues are reported, address them and produce another build. You can reuse the same build ID until you upload an archive.
3. After the archive has been successfully validated, click Distribute App.

*Xcode will create a directory containing an IPA of your app and an ExportOptions.plist file. You can create new IPAs with the same options without launching Xcode by running flutter build ipa --export-options-plist=path/to/ExportOptions.plist*






# CHAPTER 7
# DEEP LINKING

- [codewithandrea deeplinking](https://codewithandrea.com/articles/flutter-deep-links/)

## Types of Deep Links

Mobile apps can handle two types of deep links, based on the scheme they use.

#### Custom Schemes
__yourScheme://your-domain.com__

In Android, this is called a deep link, while in the iOS universe, it's called a custom URL scheme. This method is handy if you don't own a domain but want to tap into the power of deep links.

You can pick any custom scheme you like as long as you define it in your app. The setup is really quick since you don't have to worry about uniqueness.

But the downside is that it's less secure because any app could hijack your custom scheme and attempt to open your links. Plus, if someone doesn't have your app and clicks on a custom scheme link, they'll hit a dead end with an error message. While it's not the best practice, its simplicity makes it handy for quick tests.


#### HTTP/HTTPS Scheme
__https://your-domain.com__

Known as App Links on Android and Universal Links on iOS, this method provides the most secure way of adding deep link support to your mobile app.

It requires you to own a domain and perform verification on both ends. You must register your domain within the app code (manifest file on Android and Associated Domains on iOS) and verify your mobile application on the server side.

### Setting Up Deep Links on Android

- Open the android/app/src/main/AndroidManifest.xml file
- Add an intent filter to your activity tag

```xml
<intent-filter android:autoVerify="true">
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />

  <data android:scheme="https" />
  <data android:host="yourDomain.com" />
</intent-filter>
```

If you want to use a custom scheme, and open links like yourScheme://yourDomain.com, use:

```xml
<data android:scheme="yourScheme" />
<data android:host="yourDomain.com" />
```

The example above doesn't call out any specific paths. With this setup, your app can open all URLs from your domain. This is often not desirable, and if you want more control, you need to define the accepted paths by adding relevant path tags. The most common ones are path, pathPattern, and pathPrefix.

```xml
<!-- exact path value must be "/login/" -->
<data android:path="/login/" />  

<!-- path must start with "/product/" and then it can contain any number of characters; in this case, it would probably be a product ID. -->
<data android:pathPattern="/product/.*" />

<!-- path must start with /account/, so it may be /account/confirm, /account/verify etc. -->
<data android:pathPrefix="/account/" />
```

### Android Server Verification

__*Keep in mind that this step is only necessary for HTTP/HTTPS links and not for custom schemes.*__


#### assetlinks.json

```json
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example",
    "sha256_cert_fingerprints":
    ["00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00"]
  }
}]
```

Upload the assetlinks.json file to your website. It must be accessible via https://yourdomain/.well-known/assetlinks.json.

To check if the assetlinks.json is correctly set up, use the statement list tester tool provided by Google at https://developers.google.com/digital-asset-links/tools/generator

You can generate your app's SHA256 fingerprint with this command:

keytool -list -v -keystore <keystore path> -alias <key alias> -storepass <store password> -keypass <key password>

keytool -list -v \
  -keystore ~/.android/debug.keystore \
  -alias androiddebugkey \
  -storepass android \
  -keypass android


Or, if you sign your production app via Google Play Store, you can find it in the Developer Console at Setup > App Integrity > App Signing.


### How to Test Deep Links on Android

For HTTP/HTTPS links:

```bash
adb shell am start -a android.intent.action.VIEW \
  -c android.intent.category.BROWSABLE \
  -d https://yourDomain.com \
  <package name>
```
And for custom schemes:

```bash
adb shell am start -a android.intent.action.VIEW \
  -c android.intent.category.BROWSABLE \
  -d yourScheme://yourDomain.com \
  <package name>
```


If tapping the URL opens your app "inside" the source app (like Gmail or WhatsApp), rather than its own window, add android:launchMode="singleTask" inside your <activity> tag.


singleTop vs singleTask
singleTop (current):

- Creates a new instance if activity isn't at the top of the stack
- If already at top, calls onNewIntent() instead of creating new instance
- Multiple instances can exist in different tasks
- Best for: Deep linking, notifications (your use case)

singleTask:

- Only one instance exists across all tasks
- Always brings existing instance to front, clearing activities above it
- Calls onNewIntent() when relaunched
- Creates its own task if needed
- ⚠️ Destructive: Clears back stack above it

singleInstancePerTask (API 28+):  
- One instance per task


### IOS
create a file .well-known/apple-app-site-association

for example.

```json
{
  "applinks": {
    "apps": [],
    "details": [
      {
        "appIDs": ["T7L52JAWMH.com.finance.walletwise"],
        "components": [
          { "/": "/home/amount-to-send-to-tag*" },
          { "/": "/home/referral*" }
        ]
      },
      {
        "appIDs": ["T7L52JAWMH.com.finance.walletwise.dev"],
        "components": [
          { "/": "/home/amount-to-send-to-tag*" },
          { "/": "/home/referral*" }
        ]
      },
      {
        "appIDs": ["T7L52JAWMH.com.finance.walletwise.staging"],
        "components": [
          { "/": "/home/amount-to-send-to-tag*" },
          { "/": "/home/referral*" }
        ]
      }
    ]
  }
}
```

in xcode -> associated domains -> applinks:bilalmbaka.github.io.

or ios/Runner/Runner.entitlements

```xml
<dict>
	<key>aps-environment</key>
	<string>development</string>
	<key>com.apple.developer.associated-domains</key>
	<array>
		<string>applinks:bilalmbaka.github.io</string>
	</array>
</dict>
```

testing url

https://app-site-association.cdn-apple.com/a/v1/bilalmbaka.github.io




## Handling taps using navigator 2.0

### Go routers

```dart
 return MaterialApp.router(
          restorationScopeId: AppConstants.appRestorationScopeId,
          title: AppStrings.appName,
          debugShowCheckedModeBanner: false,
          routerConfig: goRouterProvider.value,
          theme: (themeProvider) ? darkTheme : lightTheme,
 )
```



# CHAPTER 8
# ANALYTICS

## FIREBASE

```bash
> flutter pub add firebase_analytics
```

```dart
FirebaseAnalytics analytics = FirebaseAnalytics.instance;
```

### Logging events
[Documentation] (https://firebase.google.com/docs/analytics/events?platform=flutter)

```dart
await FirebaseAnalytics.instance
  .logBeginCheckout(
    value: 10.0,
    currency: 'USD',
    items: [
      AnalyticsEventItem(
        itemName: 'Socks',
        itemId: 'xjw73ndnw',
        price: '10.0'
      ),
    ],
    coupon: '10PERCENTOFF'
  );
```

```dart
await FirebaseAnalytics.instance.logEvent(
    name: "select_content",
    parameters: {
        "content_type": "image",
        "item_id": itemId,
    },
);
```

_Set default event parameters_

You can log parameters across events using setDefaultEventParameters(). 
Default parameters are associated with all future events that are logged.

As with custom parameters, register the default event parameters to ensure 
they appear in Analytics reports.

Valid parameter values are String and num. Setting a key's value to null 
clears that parameter. Passing in a null value clears all parameters.

```dart
// Not supported on web
await FirebaseAnalytics.instance
  .setDefaultEventParameters({
    version: '1.2.3'
  });
```

If a parameter is specified in the logEvent() or log- method, that value 
is used instead of the default.

To clear a default parameter, call the setDefaultEventParameters() method 
with the parameter set to null.

#### [GA4] Recommended events

[Recomended events](https://support.google.com/analytics/answer/9267735)







