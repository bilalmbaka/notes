Practise questions

1. how do you access string resources.
2. create a login screen.


-----------------
Section 1
Kotlin
-----------------

Lamnda Functions
------------------

A lambda function in Kotlin is basically an anonymous function — a function without a name.
It lets you write small blocks of code that you can pass around as a value.

General syntax:

val lambdaName: (Int, Int) -> Int = { a, b -> a + b }

(Int, Int) -> Int means: it takes two Ints, returns an Int.

{ a, b -> a + b } is the lambda (parameters on the left of ->, body on the right).


Example 2
-----------

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


------------------
SECTION 2
Jetpack compose
------------------

CHAPTER 1
---------------
INRODUCITON
-----------------

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

![Image showing the activity life cycle](/native_android_images/the_activity_lifecycle.png)

***Note: The asterisk on the onRestart() method indicates that this method is not called every 
time the state transitions between Created and Started. It is only called if onStop() was 
called and the activity is subsequently restarted.***




###Chapter 3
###Themeing

##setting border radius of buttons globally.

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


