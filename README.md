# iOS-Dev-Notes
A place to drop some iOS development related notes.

## Ideas to keep in mind
* Composition over inheritance

## General tips
* Enums
* nil coalescing
* Strong layout constraint
	* Weak Layout constraint outlets will be lost if set to be inactive. If want to change constraint.active, make sure it's a strong outlet
* @testable
	* In unit test cases, do @testable import victorious to expose files to the testing target
* Use a weak reference whenever it is valid for that reference to become nil at some point during its lifetime. Conversely, use an unowned reference when you know that the reference will never be nil once it has been set during initialization.

## Swift Best Practice
* Variable names
	* Do not use any form of Hungarian notation (e.g. k for constants, m for methods), instead use short concise names and use Xcode's type Quick Help (⌥ + click) to discover a variable's type. Similarly do not use SNAKE\_CASE.
* Converting Instances
	* When creating code to convert instances from one type to another, use init() methods:
```
extension NSColor {
	    convenience init(\_ mood: Mood) {
	        super.init(color: NSColor.blueColor)
	    }
	} 
	```
* Singletons are simple in Swift:
```	
class ControversyManager {
	    static let sharedInstance = ControversyManager()
	}
```
