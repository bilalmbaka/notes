# DEEP LINKING.

## Setup.

- [codewithandrea deeplinking](https://codewithandrea.com/articles/flutter-deep-links/)

## Types of Deep Links

Mobile apps can handle two types of deep links, based on the scheme they use.

#### Custom Schemes
__yourScheme://your-domain.com__

In Android, this is called a deep link, while in the iOS universe, it's called a 
custom URL scheme. This method is handy if you don't own a domain but want to tap 
into the power of deep links.

You can pick any custom scheme you like as long as you define it in your app. 
The setup is really quick since you don't have to worry about uniqueness.

But the downside is that it's less secure because any app could hijack your custom 
scheme and attempt to open your links. Plus, if someone doesn't have your app and 
clicks on a custom scheme link, they'll hit a dead end with an error message. 
While it's not the best practice, its simplicity makes it handy for quick tests.


#### HTTP/HTTPS Scheme
__https://your-domain.com__

Known as App Links on Android and Universal Links on iOS, this method provides 
the most secure way of adding deep link support to your mobile app.

It requires you to own a domain and perform verification on both ends. 
You must register your domain within the app code (manifest file on Android 
and Associated Domains on iOS) and verify your mobile application on the server side.

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

The example above doesn't call out any specific paths. With this setup, your app can open 
all URLs from your domain. This is often not desirable, and if you want more control,
 you need to define the accepted paths by adding relevant path tags. 
 The most common ones are path, pathPattern, and pathPrefix.

```xml
<!-- exact path value must be "/login/" -->
<data android:path="/login/" />  

<!-- path must start with "/product/" and then it can contain 
any number of characters; in this case, it would probably be a product ID. -->
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

//example.

[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.finance.walletwise.app",
    "sha256_cert_fingerprints":
    [
      "3F:F8:47:EB:37:25:EA:10:B7:D9:82:DE:94:B9:5E:4D:56:BA:5D:90:7F:52:24:C2:9B:95:4B:18:B5:F0:0B:24",
      "DA:7C:89:87:22:D3:41:32:65:6E:46:41:92:2A:C5:76:6B:9D:6D:BB:E3:88:E5:36:9C:10:CC:CE:10:A3:DD:22"

    ]
  }
},
{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.finance.walletwise.dev",
    "sha256_cert_fingerprints":
    [
        "3F:F8:47:EB:37:25:EA:10:B7:D9:82:DE:94:B9:5E:4D:56:BA:5D:90:7F:52:24:C2:9B:95:4B:18:B5:F0:0B:24",
        "DA:7C:89:87:22:D3:41:32:65:6E:46:41:92:2A:C5:76:6B:9D:6D:BB:E3:88:E5:36:9C:10:CC:CE:10:A3:DD:22"
    ]
  }
}]

```



Upload the assetlinks.json file to your website. 
It must be accessible via https://yourdomain/.well-known/assetlinks.json.

To check if the assetlinks.json is correctly set up, use the statement 
list tester tool provided by Google at 
https://developers.google.com/digital-asset-links/tools/generator

You can generate your app's SHA256 fingerprint with this command:

```bash
keytool -list -v -keystore <keystore path> \
    -alias <key alias> -storepass <store password> -keypass <key password>

keytool -list -v \
  -keystore ~/.android/debug.keystore \
  -alias androiddebugkey \
  -storepass android \
  -keypass android
```

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


If tapping the URL opens your app "inside" the source app (like Gmail or WhatsApp), 
rather than its own window, add android:launchMode="singleTask" inside your <activity> tag.


**singleTop vs singleTask**

**_singleTop (default):_**

- Creates a new instance if activity isn't at the top of the stack
- If already at top, calls onNewIntent() instead of creating new instance
- Multiple instances can exist in different tasks
- Best for: Deep linking, notifications.

**_singleTask:_**

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
