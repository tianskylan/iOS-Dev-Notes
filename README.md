# iOS-Dev-Notes
A place to drop some iOS development related notes.

## Architecture
### Composition over inheritance
*"The takeaway is that, if you’re considering making an inheritance hierarchy with lots of superclasses and subclasses, try using protocols instead."*

### MVVM
![artsy MVVM image](http://artsy.github.io/images/2015-09-24-mvvm-in-swift/mvvm.png)

* MVVM separates the *Presentation logic* from the ViewController
* MVVM makes the presentation logic more testable
* MVVM is made better with a binding system(KVO, or ReactiveCocoa)

### Presentation Control
Small classes that control the binding between the model and the views is the answer: Presentation Controls. The main difference between MVVM classes and Presentation Controls is that the latter does know about UIView classes. Presentation Controls are like mini View Controllers that are only responsible for the presentation.

## Swift Best Practice
### Variable names
Do not use any form of Hungarian notation (e.g. k for constants, m for methods), instead use short concise names and use Xcode's type Quick Help (⌥ + click) to discover a variable's type. Similarly do not use SNAKE\_CASE.

### Converting Instances
When creating code to convert instances from one type to another, use init() methods:

``` swift
extension NSColor {
	convenience init(_ mood: Mood) {
		super.init(color: NSColor.blueColor)
	}
}
```

### Singletons
They are simple in Swift, but think about the design before using them:

``` swift
class ControversyManager {
	static let sharedInstance = ControversyManager()
}
```

### Dynamic dispatch rules for protocol extensions (Polymorphism)
* IF the inferred type of a variable is the protocol:
  * AND the method is defined in the original protocol
    * THEN the runtime type’s implementation is called, irrespective of whether there is a default implementation in the extension.
  * AND the method is not defined in the original protocol,
    * THEN the default implementation is called.
* ELSE IF the inferred type of the variable is the type
  * THEN the type’s implementation is called.

### Lazy Initialization
Simple way

``` swift
lazy var players = [String]()
```

With some logic

``` swift
lazy var players: [String] = {
	var temporaryPlayers = [String]()
	temporaryPlayers.append("John Doe")
	return temporaryPlayers
}()
```

Or you can lazily initialize the `var` with a instance function or class function too.

## Closure Syntax Cheetsheet
How Do I Declare a Closure in Swift?

As a variable:

```swift
var closureName: (parameterTypes) -> (returnType)
```

As an optional variable:

```swift
var closureName: ((parameterTypes) -> (returnType))?
```

As a type alias:

```swift
typealias closureType = (parameterTypes) -> (returnType)
```

As a constant:

```swift
let closureName: closureType = { ... }
```

As an argument to a function call:

```swift
func({ parameterTypes) -> (returnType) in statements})
```

As a function parameter:

```swift
array.sort({ (item1: Int, item2: Int) -> Bool in return item1 < item2 })
```

As a function parameter with implied types:

```swift
array.sort({ (item1, item2) -> Bool in return item1 < item2 })
```

As a function parameter with implied return type:

```swift
array.sort({ (item1, item2) in return item1 < item2 })
```

As the last function parameter:

```swift
array.sort { (item1, item2) in return item1 < item2 }
```

As the last parameter, using shorthand argument names:

```swift
array.sort { return $0 < $1 }
```

As the last parameter, with an implied return value:

```swift
array.sort { $0 < $1 }
```

As the last parameter, as a reference to an existing function:

```swift
array.sort(<)
```

As a function parameter with explicit capture semantics:

```swift
array.sort({ [unowned self] (item1: Int, item2: Int) -> Bool in return item1 < item2 })
```

As a function parameter with explicit capture semantics and inferred parameters / return type:

```swift
array.sort({ [unowned self] in return item1 < item2 })
```


## Additional tips
* Use Enums, Structs and Protocols
* `nil` coalescing (`??` in swift)
* Strong layout constraint
  * Weak Layout constraint outlets will be lost if set to be inactive. If want to change constraint.active, make sure it's a strong outlet
* `@testable`
  * In unit test cases, do `@testable import victorious` to expose files to the testing target
* Use a `weak` reference whenever it is valid for that reference to become nil at some point during its lifetime. Conversely, use an unowned reference when you know that the reference will never be nil once it has been set during initialization.
* A `defer` statement removes any chance of forgetting to clean up after ourselves while also simplifying our code. Even though the defer block comes immediately after the call to `alloc()`, its execution is delayed until the end of the current scope:

```swift
func resizeImage(url: NSURL) -> UIImage? {
    // ...
    let dataSize: Int = ...
    let destData = UnsafeMutablePointer<UInt8>.alloc(dataSize)
    defer {
        destData.dealloc(dataSize)
    }
    var destBuffer = vImage_Buffer(data: destData, ...)
    // scale the image from sourceBuffer to destBuffer
    var error = vImageScale_ARGB8888(&sourceBuffer, &destBuffer, ...)
    guard error == kvImageNoError
        else { return nil }
    // create a CGImage from the destBuffer
    guard let destCGImage = vImageCreateCGImageFromBuffer(&destBuffer, &format, ...)
        else { return nil }
    // ...
}
```

* Before hardcoding some value to 0, check to see if that type has some other initial value available. For example, `PHImageRequestID` has `PHInvalidImageRequestID`, so use that instead of 0.
* Log Specific Constraints

```swift
/// When debugging complex layouts, it can sometimes be helpful to look at
/// only the constraints involving a specific problem view or area.
/// We can use this function to grab an array of the constraints
/// affecting a particular axis.

profileHeaderView.constraintsAffectingLayoutForAxis(.Vertical)
```

* UIImageRenderingMode

```swift
typedef NS_ENUM(NSInteger, UIImageRenderingMode) {
    UIImageRenderingModeAutomatic,          // Use the default rendering mode for the context where the image is used
    UIImageRenderingModeAlwaysOriginal,     // Always draw the original image, without treating it as a template
    UIImageRenderingModeAlwaysTemplate,     // Always draw the image as a template image, ignoring its color information
} NS_ENUM_AVAILABLE_IOS(7_0);
```

Then simply do,

```swift
if var imageToChange = imageView.image?.imageWithRenderingMode(.AlwaysTemplate) {
    imageView.image = imageToChange
    imageView.tintColor = .redColor() //Setting the tint color is what changes the color of the image itself!
}
```

The `UIImageRenderingMode` can be set in storyboard too.

* vector PDFs as assets can be used to generate icons at different sizes, @1x, @2x, @3x...
* Assets in the asset catalog can be sliced so it knows how to strech itself. For example, it will be able to reserve its round corners when being streched.

## Tech talk notes
### AVKit
**AVAsset**

* Load necessary properties and wait to be loaded before calling setter
* Only load properties you need
* Request properties needed in batches

**AVPlayer & AVPlayerItem**

* KVO for property changes
* Do not rely on the ordering of events (Use KVO; set up item before associate to player)
* Serialize access to objects on main queue
& Set up player item before adding it to player

### Mysteries of Autolayout
**Use stack view when appropriate**

* You can stack the stack views too. Pretty neat

**Layout engine**

* Activate and deactivate instead of Add and Remove constraints
* Never deactivate everything in self.view.constraints
* Keep a reference to the constraints you are going to change
* You can animate the change of constraints (May need layoutIfNeeded call)

**View Sizing**

* Use constraints instead of hard-coded values
* Override intrinsicContentSize for specific reasons
	* size information does not come from constraints
	* If view has custom drawing (sometimes)
	* You will be responsible for invalidating

**Self sizing Table view cells**

* Define size fully in constraints

**Priorities**

* Can help keep constraints from unsatisfiability
* But look out for competing priorities!
* Results are more consistent
* Use content priorities to get to the right layout
	* Hugging priorities hug content
	* Compression resistance resists squishing

**Alignment**

* First and last baseline for better aligned text
* Leading and trailing instead of left and right
* Override alignmentRectInsets to adjust alignment rects
