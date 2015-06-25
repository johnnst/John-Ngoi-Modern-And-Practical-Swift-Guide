# John-Ngoi-Modern-And-Practical-Swift-Guide

As part of personal learning, and to help other iOS developers and teams out there, I took the time to write this guide with the following themes in mind.

1. Readability
2. Modern
3. Support team development

As Swift is a new language, I welcome pull requests. There is still a lot of maturing to do, and if we keep the goals we set out in mind, we can build awesome apps that scale and have fewer bugs.

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Swift Programming Language](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/index.html)
* [Swift Blog](https://developer.apple.com/swift/blog/)
* [Using Swift with Cocoa and Objective-C](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Naming](#naming)
* [Spacing](#spacing)
* [MARK](#mark)
* [Ternary Operator](#ternary-operator)
* [Optionals](#optionals)
* [Methods and Optionals](#methods-and-optionals)
* [Error handling](#error-handling)
* [Comments](#comments)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerations](#enumerations)
* [Models](#models)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Run in background](#run-in-background)
* [Run in main thread](#run-in-main-thread)
* [Run Once](#run-once)
* [Singletons](#singletons)
* [Algorithms](#algorithms)
* [Logging](#logging)
* [Xcode Project](#xcode-project)

## Naming
Use descriptive names with camel case for classes, methods, variables, etc. Class names should be Capitalized, and method names and variables should start with a lower case letter.

**For example:**
```swift
class SomeClass {
let someConstant = 100
var someString: String?
var awesomeButton: UIButton?
var prettyPhoto: UIImageView?
}
```

For functions and init methods, it is preferred to provide named parameters for all arguments. Include external parameter names if it makes it more readable or solves scope naming conflicts.

**For example:**
```swift
func stringFromInt(someInt: Int) -> String
func stringFromInt(#someInt: Int) -> String
func stringFromInt(convert someInt: Int) -> String
func stringFromInt(someInt: Int = 888) -> String
```

For new classes, use the project prefix in the name. For example, we use D3.

```swift
@objc protocol D3SomeAwesomeViewControllerDelegate {
// some awesome func
}

class D3SomeAwesomeViewController {

// do awesome stuff
}
```

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.
* To ensure a block of code is indented correctly, you can select the lines of code and hit CTRL + I to let Xcode automatically indent the lines.

**For example:**
```swift
if someString.isEmpty {
someString = "hello world"
} else {
println("\(someString)")
}  
```

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* For parameter types, place one white space after the colon to help with readability and one whitespace before and after -> .

**For example:**
```swift
var someString: String?
func convertIntToString(someInt: Int) -> String
```

**Not:**
```swift
var someString:String?
func convertIntToString(someInt:Int)->String
```

## MARK

`//MARK: ` should always be used to group methods into functional or logical sections. Below are usual `//MARK: `s.

`//MARK: View Lifecycle`
viewDidLoad
viewWillAppear
didReceiveMemoryWarning

`//MARK: IB Methods`
Any method from Interface Builder.

`//MARK: Private Methods`
Private methods.

`//MARK: Service Methods`
Methods that are meant to provide some sort of a service.

`//MARK: Utilities`
Methods that provide utilities such as serialization, calculation, formatting, etc.

`//MARK: Handle Notifications`
Methods that handle notifications from NSNotificationCenter.

`//MARK: Handle Gestures`
Methods that handle gestures such as tap and swipe.

When using a delegate or datasource, just copy and paste the actual name into the MARK. This allows you to `Option + Click` it for the documentation within the MARK.

`//MARK: UITableViewDelegate`

`//MARK: UITableViewDataSource`

`//MARK: UIAlertViewDelegate`

`//MARK: UIScrollViewDelegate`

`//MARK: SomeCustomDelegate`


### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**
```swift
result = a > b ? x : y;
```

**Not:**
```swift
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

Error handling in Swift is somewhat up in the air. Here's a pattern to use for now.

**For example:**
```swift
enum Result<T> {
case Success(T)
case Failure(String)
}
```
If you are simply handling NSError objects, you can do this.

**For example:**
```swift
var error: NSError? = nil
var jsonResult: NSDictionary = NSJSONSerialization.JSONObjectWithData(someData!, options: NSJSONReadingOptions.MutableContainers, error: &error) as NSDictionary
if let err = error {
// handle error here

// provide exit if needed
return
}

if let json = jsonResult {
// always check for the actual result, as NSError is not always reliable
}

```

## Optionals

Understanding and mastering optionals is key to migrating from Objective C to Swift. You will pretty much use it in a lot of places. 

Below is an extract from the Swift Programming Guide that you should read before proceeding.

```guide
Swift also introduces optional types, which handle the absence of a value. Optionals say either “there is a value, and it equals x” or “there isn’t a value at all”. Optionals are similar to using nil with pointers in Objective-C, but they work for any type, not just classes. Optionals are safer and more expressive than nil pointers in Objective-C and are at the heart of many of Swift’s most powerful features.

Optionals are an example of the fact that Swift is a type safe language. Swift helps you to be clear about the types of values your code can work with. If part of your code expects a String, type safety prevents you from passing it an Int by mistake. This restriction enables you to catch and fix errors as early as possible in the development process.
```

In addition, read up on Optional Chaining here.

[Optional Chaining](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/OptionalChaining.html)


## Methods and Optionals

When defining methods, use optionals in the parameters for safety. This avoids your app from crashing if a piece of code unintentionally passes nil to a non-optional parameter and you handle the optional properly when using the parameter within the method scope.

**For example:**

```swift
func convertStringToInt (input: String?) -> Int? {
if let value = input {
let newValue = value.toInt()
return newValue
}
return nil
}
```

**Not:**

```swift
func convertStringToInt (input: String) -> Int {
if let newValue = input.toInt() {
return newValue
}
return 0
}
```

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted. Long comments should generally be avoided, as code should be as self-documenting as possible with only the need for brief descriptions. 

We will be using appledoc for generating documentation, so the following syntax is required for comments included in documentation generation.

**Syntax**

```swift
/** 

To be updated ...

*/
```
The foward slash and double asterisks will be picked up by appledoc for documentation generation.

The following comment syntax will not be picked up by appledoc and can still be used for comments that should not be included in documentation generation.
```swift
/* comment */

// comment
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```swift
let frame = self.view.frame;

let x = CGRectGetMinX(frame);
let y = CGRectGetMinY(frame);
let width = CGRectGetWidth(frame);
let height = CGRectGetHeight(frame);
```

**Not:**

```swift
let frame = self.view.frame;

let x = frame.origin.x;
let y = frame.origin.y;
let width = frame.size.width;
let height = frame.size.height;
```

We are somewhat flexible on this, however, use CGRect functions to get MaxX or MaxY for example. Anything that requires computation and provided by CGRect functions.

## Constants

In Swift, to declare a static property, you have to declare it in a struct or enum.

```swift
struct Sections {
static let feedback = 0
static let generalInformation = 1
static let developerSettings = 2
}

struct Rows {
static let tellUsWhatYouThink = 0
static let reportAProblem = 1

static let checkForUpdates = 0
static let termsAndConditions = 1
static let signOut = 2

static let credentialsAndEnvironment = 0
}
```

It is also covered on this page of the Swift Programming Guide [Properties](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html)

This is still preferred over the #define macro.


## Enumerations

Enumerations are much more powerful in Swift vs Objective C. 

Below are a few example uses for it.

**For example:**

```swift
enum ImageViewModes: Int {
case None = 0
case DoubleTapToZoom = 1
case PinchToZoom = 2
}
```

```swift
private enum Uploadable {
case Data(NSURLRequest, NSData)
case File(NSURLRequest, NSURL)
case Stream(NSURLRequest, NSInputStream)
}
```

```swift
public enum Method: String {
case OPTIONS = "OPTIONS"
case GET = "GET"
case HEAD = "HEAD"
case POST = "POST"
case PUT = "PUT"
case PATCH = "PATCH"
case DELETE = "DELETE"
case TRACE = "TRACE"
case CONNECT = "CONNECT"
}
```

Read up on it here.

[Enumerations](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html#//apple_ref/doc/uid/TP40014097-CH12-XID_221)

## Models

Creating models in Swift is much more enjoyable vs Objective C style.

**For example:**

```swift
class Person {
var personId: Int?
var firstName: String?
var lastName: String?
var awesomePerson: Bool?

init(firstName: String, lastName: String, awesomePerson: Bool = true) {
self.personId = SomeMagicalIdGenerator()
self.firstName = firstName
self.lastName = lastName
self.awesomePerson = awesomePerson
}
}
```

## Private Properties

Swift has 3 access levels; public, internal, and private. The default access is internal.

**For example:**

```swift
public class SomePublicClass {}
internal class SomeInternalClass {}
private class SomePrivateClass {}

public var somePublicVariable = 0
internal let someInternalConstant = 0
private func somePrivateFunction() {}
```

Read up on the [Access Levels](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html).

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.


## Run in background

When you need to run something in the background, use GCD.

**For Example:**

```swift
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), { () -> Void in
sleep(3)
println("slept for 3 seconds")
})
```

In addition, do not create a serial queue when you do not need it, just use the example above.

## Run in main thread

When you need to run something on main thread, use GCD. Useful for performing UI functions.

**For Example:**

```swift
dispatch_async(dispatch_get_main_queue(), { () -> Void in
println("calling gcd main thread")
})
```

## Run Once

dispatch_once is a great way to avoid executing the same block of code more than once if the outcome is always the same, or we need to keep a single instance of an object regardless of instantiations. A good example is the use of dispatch_once for the Singleton Pattern.

**For Example:**

```swift
class MainClass {

class var someValue: String {

struct Static {
static var token: dispatch_once_t = 0
static var value: String?
}

dispatch_once(&Static.token, { () -> Void in
Static.value = "new value, never changes and executed only once"
})

return Static.value!
}
}
```


## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```swift
class Singleton {
class var sharedInstance: Singleton {
struct Static {
static var instance: Singleton?
static var token: dispatch_once_t = 0
}

dispatch_once(&Static.token) {
Static.instance = Singleton()
}
return Static.instance!
}
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Algorithms

Avoid implementing your own algorithms for common tasks; sorting, finding. Use the functions from the Swift library to perform these tasks.

[Algorithms](https://developer.apple.com/library/ios/documentation/General/Reference/SwiftStandardLibraryReference/Algorithms.html#//apple_ref/doc/uid/TP40014608-CH15-SW1)

## Logging

println is the default console log output function. However, to have this running in production is both unnecessary and can create performance and security issues.

We have created a logging macro that only generates output when the DEBUG macro exists and also includes the function name and line number. 

Avoid using println and use dlog only.

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

