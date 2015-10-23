# iOS-Dev-Notes
A place to drop some iOS development related notes.

## Swift Best Practice
### Composition over inheritance
*"The takeaway is that, if you’re considering making an inheritance hierarchy with lots of superclasses and subclasses, try using protocols instead."*

### Variable names
Do not use any form of Hungarian notation (e.g. k for constants, m for methods), instead use short concise names and use Xcode's type Quick Help (⌥ + click) to discover a variable's type. Similarly do not use SNAKE\_CASE.

### Converting Instances
When creating code to convert instances from one type to another, use init() methods:

```swift
extension NSColor {
	convenience init(_ mood: Mood) {
		super.init(color: NSColor.blueColor)
	}
}
```

### Singletons
They are simple in Swift, but think about the design before using them:

```swift
class ControversyManager {
	static let sharedInstance = ControversyManager()
}
```

### Dynamic dispatch rules for protocol extensions (polymorphism)
* IF the inferred type of a variable is the protocol:
	* AND the method is defined in the original protocol
		* THEN the runtime type’s implementation is called, irrespective of whether there is a default implementation in the extension.
	* AND the method is not defined in the original protocol,
		* THEN the default implementation is called.
* ELSE IF the inferred type of the variable is the type
	* THEN the type’s implementation is called.


## General tips
* Enums
* nil coalescing
* Strong layout constraint
	* Weak Layout constraint outlets will be lost if set to be inactive. If want to change constraint.active, make sure it's a strong outlet
* @testable
	* In unit test cases, do @testable import victorious to expose files to the testing target
* Use a weak reference whenever it is valid for that reference to become nil at some point during its lifetime. Conversely, use an unowned reference when you know that the reference will never be nil once it has been set during initialization.
* A `defer` statement removes any chance of forgetting to clean up after ourselves while also simplifying our code. Even though the defer block comes immediately after the call to alloc(), its execution is delayed until the end of the current scope:

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

## Closure Syntax Cheetsheet
How Do I Declare a Closure in Swift?

```swift
As a variable:
var closureName: (parameterTypes) -> (returnType)

As an optional variable:
var closureName: ((parameterTypes) -> (returnType))?

As a type alias:
typealias closureType = (parameterTypes) -> (returnType)

As a constant:
let closureName: closureType = { ... }

As an argument to a function call:
func({(parameterTypes) -> (returnType) in statements})

As a function parameter:
array.sort({ (item1: Int, item2: Int) -> Bool in return item1 < item2 })

As a function parameter with implied types:
array.sort({ (item1, item2) -> Bool in return item1 < item2 })

As a function parameter with implied return type:
array.sort({ (item1, item2) in return item1 < item2 })

As the last function parameter:
array.sort { (item1, item2) in return item1 < item2 }

As the last parameter, using shorthand argument names:
array.sort { return $0 < $1 }

As the last parameter, with an implied return value:
array.sort { $0 < $1 }

As the last parameter, as a reference to an existing function:
array.sort(<)

As a function parameter with explicit capture semantics:
array.sort({ [unowned self] (item1: Int, item2: Int) -> Bool in return item1 < item2 })

As a function parameter with explicit capture semantics and inferred parameters / return type:
array.sort({ [unowned self] in return item1 < item2 })
```
