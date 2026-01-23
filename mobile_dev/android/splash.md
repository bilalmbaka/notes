Sources:

[Google developers](https://developer.android.com/develop/ui/views/launch/splash-screen)
[Suraj Maity Blog post](https://surajmyt.hashnode.dev/splash-screen-in-jetpack-compose)
[Elozino Ovedhe Blog post](https://dev.to/elozino/getting-started-with-splash-screen-in-jetpack-compose-144l)

Starting in Android 12, the SplashScreen API lets apps launch with animation,
including an into-app motion at launch, a splash screen showing your app icon,
and a transition to your app itself. A SplashScreen is a Window and therefore covers
an Activity.

1. Enter animation: this consists of the system view to the splash screen. It is
controlled by the system and isn't customizable.

2. Splash screen (shown during the "wait" portion of the sequence): the splash screen 
can be customized, letting you supply your own logo animation and branding. 
It must meet the requirements described in this page to work properly.

3. Exit animation: this consists of the animation that hides the splash screen. 
If you want to customize it, use the SplashScreenView and its icon. 
You can run any animation on them, with settings for transform, opacity, 
and color. In this case, manually remove the splash screen when the animation is done.


### Splash screen animation requirements

Your splash screen must adhere to the following specifications:

- Set a single window background color with no transparency. Day and Night mode 
are supported with the SplashScreen compat library.

Make sure the animated icon meets the following specifications:

- Format: the icon must be an AnimatedVectorDrawable (AVD) XML.
- Dimensions: an AVD icon must be four times the size of an adaptive icon, as follows:
    The icon area must be 432 dp—in other words, four times the 108 dp area of 
    an unmasked adaptive icon.<br/>
    The inner two-thirds of the image is visible on the launcher icon, and must 
    be 288 dp—in other words, four times the 72 dp that makes up the inner masked 
    area of an adaptive icon.

- Duration: we recommend not exceeding 1,000 ms on phones. You can use a 
delayed start, but this can't be longer than 166 ms. If the app startup time is longer 
than 1,000 ms, consider a looping animation. Establish an appropriate time to dismiss 
the splash screen, which happens as your app draws its first frame. You can further 
customize this as described in the section about keeping the splash screen on-screen 
for longer periods.


