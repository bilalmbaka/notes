# Hero animations

sources:<br/>
[Flutter documentation](https://docs.flutter.dev/ui/animations/hero-animations)

The hero refers to the widget that flies between screens.
The Hero widget in Flutter implements a style of animation commonly known as shared element transitions or shared element animations.

A Route describes a page or screen in a Flutter app.

As the hero animates from the source to the destination route, the destination route (minus the hero) fades into view.

## Basic structure of a hero animation

* Use two hero widgets in different routes but with matching tags to implement the animation.
* The Navigator manages a stack containing the app's routes.
* Pushing a route on or popping a route from the Navigator's stack triggers the animation.
* The Flutter framework calculates a rectangle tween, RectTween that defines the hero's boundary as it flies from the source to the destination route. During its flight, the hero is moved to an application overlay, so that it appears on top of both routes.


Hero animations are implemented using two Hero widgets: one describing the widget in the source route, and another describing the widget in the destination route. From the user's point of view, the hero appears to be shared, and only the programmer needs to understand this implementation detail. Hero animation code has the following structure:

* Define a starting Hero widget, referred to as the source hero. The hero specifies its graphical representation (typically an image), and an identifying tag, and is in the currently displayed widget tree as defined by the source route.

* Define an ending Hero widget, referred to as the destination hero. This hero also specifies its graphical representation, and the same tag as the source hero. It's essential that both hero widgets are created with the same tag, typically an object that represents the underlying data. For best results, the heroes should have virtually identical widget trees.

* Create a route that contains the destination hero. The destination route defines the widget tree that exists at the end of the animation.

* Trigger the animation by pushing the destination route on the Navigator's stack. 

* The Navigator push and pop operations trigger a hero animation for each pair of heroes with matching tags in the source and destination routes.

* Flutter calculates the tween that animates the Hero's bounds from the starting point to the endpoint (interpolating size and position), and performs the animation in an overlay.


## Simple Hero animation.

To do a basic hero animation just wrap the desired wiget you want to animate
in a hero and make sure they both have the same tag.