Practise questions

1. how do you access string resources.
2. create a login screen.

---

Section 1
Kotlin

---

## Lamnda Functions

A lambda function in Kotlin is basically an anonymous function — a function without a name.
It lets you write small blocks of code that you can pass around as a value.

General syntax:

val lambdaName: (Int, Int) -> Int = { a, b -> a + b }

(Int, Int) -> Int means: it takes two Ints, returns an Int.

{ a, b -> a + b } is the lambda (parameters on the left of ->, body on the right).

## Example 2

Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
Greeting(
name = "Android",
modifier = Modifier.padding(innerPadding)
)
}

This is the function definition for a scaffold

@Composable
fun Scaffold(
modifier: Modifier = Modifier,
topBar: @Composable () -> Unit = {},
bottomBar: @Composable () -> Unit = {},
floatingActionButton: @Composable () -> Unit = {},
content: @Composable (PaddingValues) -> Unit
) {
// ...implementation...
}

// Explicit
Scaffold(modifier = Modifier.fillMaxSize(), content = { innerPadding ->
Greeting(name = "Android", modifier = Modifier.padding(innerPadding))
})

// Trailing lambda
Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
Greeting(name = "Android", modifier = Modifier.padding(innerPadding))
}

Traling lambda means that if the last parameter of a function is a lambda,
then you can write the lambda outside the parentheses.

---

SECTION 2
Jetpack compose

---

## CHAPTER 1

## INRODUCITON

class MainActivity : ComponentActivity() {
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
enableEdgeToEdge()
setContent {
AdsAppTheme {
Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
Greeting(
name = "Android",
modifier = Modifier.padding(innerPadding)
)
}
}
}
}
}

1. class MainActivity : ComponentActivity()

A. MainActivity is your app’s main screen (entry point).
B. It extends ComponentActivity, which is a base class for activities that want to use Jetpack Compose
instead of XML layouts.

2. override fun onCreate(savedInstanceState: Bundle?) {
   super.onCreate(savedInstanceState)

A. onCreate is called when the activity starts.
B. super.onCreate(savedInstanceState) makes sure the base setup is done.

3. enableEdgeToEdge()

A. This makes your UI draw behind system bars (status bar, navigation bar).
B. It’s optional but gives a modern full-screen look.

4. setContent { … }

A. Instead of setContentView(R.layout.something) (XML), Compose apps use setContent {}.
B. Inside, you write composable functions (UI in Kotlin code).
C. AdsAppTheme is probably your custom Material 3 theme wrapper, giving colors, typography, and shapes.

# Chapter 2

# Activity lifecycle

### sources

- [Stages of the activity life cycle (Android tutorial)](https://developer.android.com/codelabs/basic-android-kotlin-compose-activity-lifecycle?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-4-pathway-1%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-activity-lifecycle#2)

![Image showing the activity life cycle](/native_android_images/the_activity_lifecycle.png)

**_Note: The asterisk on the onRestart() method indicates that this method is not called every
time the state transitions between Created and Started. It is only called if onStop() was
called and the activity is subsequently restarted._**

- Log.v _verbose_
- Log.i _information_
- Log.d _debug_
- Log.w _warning_
- Log.e _errorMessage_

### onStart()

The onStart() lifecycle method is called just after onCreate(). After onStart() runs, your
activity is visible on the screen. Unlike onCreate(), which is called only once to initialize
your activity, onStart()can be called by the system many times in the lifecycle of your activity.

Note that onStart()is paired with a corresponding **_onStop()_** lifecycle method. If the user starts
your app and then returns to the device's home screen, the activity is stopped and is no longer
visible on screen.

When an activity starts from the beginning, you see all three of these lifecycle callbacks called in order:

- onCreate() when the system creates the app.
- onStart() makes the app visible on the screen, but the user is not yet able to interact with it.
- onResume() brings the app to the foreground, and the user is now able to interact with it.

**_Despite the name, the onResume() method is called at startup, even if there is nothing to resume._**

```kotlin
    override fun onStart() {
        super.onStart();
        Log.d(TAG,"OnStart Called");
    }

    override fun onResume() {
        super.onResume()
        Log.d(TAG, "onResume Called")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d(TAG, "onRestart Called")
    }

    override fun onPause() {
        super.onPause()
        Log.d(TAG, "onPause Called")
    }

    override fun onStop() {
        super.onStop()
        Log.d(TAG, "onStop Called")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, "onDestroy Called")
    }
```

### Configuration changes

A configuration change occurs when the state of the device changes so radically that the easiest
way for the system to resolve the change is to completely shut down and rebuild the activity,
configuration change causes onDestroy() to be called.

<ins>Lifecycle of a composable</ins> <br>

Composable functions have their own lifecycle that is independent of the Activity lifecycle.
Its lifecycle is composed of the events: enters the Composition, recomposing 0 or more times,
and then leaving the Composition.

In order for Compose to track and trigger a recomposition, it needs to know when state has changed.
To indicate to Compose that it should track an object's state, the object needs to be of type
**_State or MutableState_**. The **_State_** type is immutable and can only be read.
A **_MutableState_** type is mutable and allows reads and writes.

```kotlin
    var revenue = mutableStateOf(0)
```

While this is enough to have Compose trigger a recomposition when the revenue value changes, it is
not enough to retain its updated value. Each time the composable is reexecuted, it will reinitialize
the revenue value to its initial default value of 0.

To instruct Compose to retain and reuse its value during recompositions, you need to declare it with
the remember API.

```kotlin
    var revenue by remember { mutableStateOf(0) }
```

While Compose remembers the revenue state during recompositions, it does not retain this state during
a configuration change. For Compose to retain the state during a configuration change,
you must use **_rememberSaveable_**

```kotlin
    var revenue by rememberSaveable { mutableStateOf(0) }

    var currentDessertImageId by rememberSaveable {
        mutableStateOf(desserts[currentDessertIndex].imageId)
    }
```

# Chapter 3

# Themeing

### setting border radius of buttons globally.

```kotlin
private val AppShapes = Shapes(
    small = RoundedCornerShape(8.dp),
    medium = RoundedCornerShape(12.dp),
    large = RoundedCornerShape(20.dp) // <-- Buttons use `large` by default
)

@Composable
fun AdsAppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colorScheme = LightColors, // your colors
        typography = Typography,
        shapes = AppShapes,        // ✅ apply shapes here
        content = content
    )
}
```

small → used for small components like TextField

medium → used for medium surfaces (Cards, etc.)

large → used for large components like Button

So if you want all your buttons to have, say,
12dp rounded corners, set large = RoundedCornerShape(12.dp)
in your theme.
