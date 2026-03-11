# TABLE OF CONTENTS
* [Swift fundamentals](#swift-fundamentals)



<br/>
<br/>
<br/>

#
# SECTION 1

# Swift programming language.

# Swift fundamentals

### [weak self]

Think of [weak self] as a safety measure to prevent Memory Leaks.

In Swift, when two objects hold onto each other strongly, they 
create a "Deadlock" where neither can be deleted from the phone's 
memory. This is called a Strong Reference Cycle.


__Imagine this scenario:__

1. You start a long OCR process.
2. The user hits the "Back" button to leave the screen.

The Problem: The OCR closure is still holding a "Strong" grip on 
the Controller (self).

The Result: Even though the user left the screen, the Controller 
stays alive in the background, wasting RAM.

2. The "Weak" Solution
When you add [weak self], you are telling the closure: "You can use me if 
I'm still around, but don't force me to stay alive just for you."

Inside the closure, self becomes Optional. That’s why you see the
 question mark: self?.extractText(...).

If self still exists: The code runs.

If the user closed the screen: self becomes nil, the line is skipped, 
and no memory is wasted.


__How to use it safely__

In your OCR code, we used it like this:

```swift
channel.setMethodCallHandler({ [weak self] (call, result) in
    // self is now optional. 
    // If the app dismissed this controller, 'self' is nil and this won't crash.
    self?.handleOCR(call, result: result) 
})

//or

guard let strongSelf = self else { return }
strongSelf.doSomething()
strongSelf.doAnotherThing()
```

### Dispatch queue.

```swift
DispatchQueue.main.async { ... }
````

In Swift, DispatchQueue is the tool we use to manage Concurrency.

Think of it as a set of "conveyor belts" (queues) for tasks. Some tasks 
are heavy and slow (like OCR), and some are light and fast (like updating the UI). DispatchQueue helps you decide which "belt" a task should run on so 
your app doesn't freeze.

__The "Main" Thread vs. The "Background" Thread__

1. DispatchQueue.main (The UI Thread): This is the single conveyor 
belt responsible for everything the user sees. If you put a heavy 
task (like scanning a high-res image) on this belt, the "scrolling" 
and "button clicking" tasks get stuck behind it. The app "freezes."

2. DispatchQueue.global() (Background Threads): These are extra belts 
in the back of the factory. We send heavy math, API calls, and OCR here so 
the user can keep interacting with the app.


The .async (Asynchronous) means "Fire and Forget."
