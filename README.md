# NYTimes Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at The New York Times. We welcome your feedback in [issues](https://github.com/NYTimes/objetive-c-style-guide/issues), [pull requests](https://github.com/NYTimes/objetive-c-style-guide/pulls) and [tweets](https://twitter.com/nytimesmobile). Also, [we're hiring](http://jobs.nytco.com/job/New-York-iOS-Developer-Job-NY/2572221/).

Thanks to all of [our contributors](https://github.com/NYTimes/objective-c-style-guide/contributors).

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot-Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Naming](#naming)
  * [Underscores](#underscores)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)

## Dot-Notation Syntax

Dot-notation should be used only when accessing properties.  
When setting properties, brackets should be used.  

Dot notation should _never_ be used for methods other than properties.

####Reasons for this:

1.	    You can clearly see the type of the variable in question when you use `[]` for setters, due to Xcode's autocompletion engine
2.	    Using dot notation for setters make the reader feel that he is setting a primitive (non object) type variable

**For example:**  
```objc
[view setBackgroundColor:[UIColor orangeColor]];  
id delegate = [UIApplication sharedApplication].delegate;
```

**Not:**
```objc
view.backgroundColor = [UIColor orangeColor];
UIApplication.sharedApplication.delegate;
```

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**For example:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**For Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Do not use underscores (_) for local variable names.

Use Meaningful, descriptive words to name variables. Do not use abbreviations.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/ADBannerView *googleAdView;
```objc
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

Use appropriate prefix for the UI elements so that you can identify them from the rest of the variables.

Append computation qualifiers (`Avg`, `Sum`, `Min`, `Max`, `Index`) to the end of a variable name where appropriate. 


## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

## Booleans

Boolean variable names should be prefixed or should contain identifiers like `is`,`has`,`did`,etc which implies `Yes`/`No` or `True`/`False` values, such as `fileIsFound`.

Use a positive connotation for boolean variable names (e.g. `isOpen` as opposed to `notOpen`).

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

## Categories

All category methods must have a prefix. This is to avoid conflicts when a method in two or more categories has the same signature.

**Prefix category methods**

```objc
@interface UIButton (HVDAdditions)
+ (UIButton *)HVD_bigRedButton;
```

**Not**  

```objc
@interface UIButton (HVDAdditions)
+ (UIButton *)bigRedButton;
```

## Creating Header files

Always have the following code in the header file.

**For Example: creating a class named BaseViewController**

```objc
#ifndef BASE_VIEW_CONTROLLER
#define BASE_VIEW_CONTROLLER

//Interface and Declaration goes here...

#endif
```

This is because if the file has been expanded already, it will not expand it again!

## Pre-processor directives and Constants

1. Do not use #define for constants. Use `const` instead.

2. Use #define only when something has to be decided at compile time.

3. Use Uppercase to name constants or #define.

## Methods

Use this format for naming routines that perform some operation on a given object, such as `calculateInvoiceTotal{}`. When naming methods, include a description of the value being returned, such as `getCurrentWindowName{}`

Avoid large methods. As a method’s body approaches 20 to 30 lines of code, look for blocks that could be split into their own methods and possibly shared by other methods

## Strings

Avoid hardcoding Strings, use resource files instead or place them in a separate file defining them as constants.

- If your application supports multiple languages, then using resource files is a good idea. It makes it vastly easier to localize your application for different languages. If you want to be able to run it
in French, just give the string resources to a translator and they'll give you back a French translation.

- Hard-coded strings could be duplicated across your code and are much more difficult to "find and replace". Hence, you are prone to errors and using resource files is better option then.

## DRY Principle

1. DRY stands for Don’t Repeat Yourself.

2. The Principle states:- 
“Every single piece of knowledge must have a single, unambiguous and authoritative representation within a system.”

3. The purpose for most applications (or computers in general) is to automate repetitive tasks. This principle should be maintained in all code. The same piece of code should not be repeated over and over again.

4. If you find yourself using the same block of code more than once, it’s a good candidate for a separate method.

## Keep The Code Simple

The code that a programmer writes should be simple. Complicated logic for achieving a simple thing should be kept to a minimum since the code might be modified by another programmer in the future. The logic one programmer implemented may not make perfect sense to another. So, always keep the code as simple as possible.

## Commenting

- Do not write comments for every line of code and every variable declared.
- Write comments wherever required. Good, readable code requires fewer comments. If all variables and method names are meaningful, that would make the code very readable and will not need many comments. 
- Do not write comments if the code is easily understandable without comment. The drawback of having lot of comments is that if you change the code and forget to change the comment, it will lead to more confusion.
- The bottom line is, write clean, readable code such a way that it doesn't need any comments to understand.

- Use complete sentences when writing comments. Comments should clarify the code, not add ambiguity.
- If you have to use some complex or weird logic for any reason, document it very well with sufficient comments.

- At the beginning of every routine, it is helpful to provide standard, boilerplate comments, indicating the routine's purpose, assumptions, and limitations. A boilerplate comment should be a brief introduction that explains why it exists and what it can do.
Each major routine should have a header that identifies:
1. what it is supposed to do
2. what the parameters mean (both input and output)
3. what it returns (if it's a function)
4. any known limitations

- Mark incomplete code with `TODO:` comments. When working with many classes at once, it can be very easy to lose a train of thought.

## Other conventions and good practices

Do not use logical operators on object types:

**The right way**  

```objc  
NSError *error = theError;  
if (error != nil) ...
```

**Not**

```objc
NSError *error = theError;
if (!error) ...
```
Use one blank line to separate logical groups of code.

Declare variables as close as possible to where it is first used. 

Avoid Deep Nesting. Too many levels of nesting can make code harder to read and follow.

Limit Line Length. Our eyes are more comfortable when reading tall and narrow columns of text. This is precisely the reason why newspaper articles look like the way they are. For this reason use one variable declaration per line.
Also in methods/procedures/functions, try using one or two parameter declaration per line. The basic idea is to avoid left-right scrolling.
 
**For Ex:**

```objc
NSNumber *redBallsCount;
NSNumber *blueBallsCount;
NSNumber *greenBallsCount;

- (id)initWithBitmapDataPlanes:(unsigned char **)planes 
					pixelsWide:(NSInteger)width 
					pixelsHigh:(NSInteger)height 
					bitsPerSample:(NSInteger)bps 
					samplesPerPixel:(NSInteger)spp 
					hasAlpha:(BOOL)alpha 
					isPlanar:(BOOL)isPlanar 
					colorSpaceName:(NSString *)colorSpaceName 
					bitmapFormat:(NSBitmapFormat)bitmapFormat 
					bytesPerRow:(NSInteger)rowBytes 
					bitsPerPixel:(NSInteger)pixelBits {
						
	//code goes here...					
}
```

**Not**

```objc

NSNumber *redBallsCount, *blueBallsCount, *greenBallsCount;

- (id)initWithBitmapDataPlanes:(unsigned char **)planes pixelsWide:(NSInteger)width pixelsHigh:(NSInteger)height bitsPerSample:(NSInteger)bps samplesPerPixel:(NSInteger)spp hasAlpha:(BOOL)alpha isPlanar:(BOOL)isPlanar colorSpaceName:(NSString *)colorSpaceName bitmapFormat:(NSBitmapFormat)bitmapFormat bytesPerRow:(NSInteger)rowBytes bitsPerPixel:(NSInteger)pixelBits {
	
	//code...
}
```

Always watch for unexpected values. For example, if you are using a parameter with 2 possible values, never assume that if one is not matching then the only possibility is the other value. 

**For Ex:**

```objc
if ( memberType == eMemberTypes.registered )
{
// Registered user… do something…
}
else if ( memberType == eMemberTypes.guest )
{
// Guest user... do something…
}
else
{
// Un expected user type. Throw an //exception
//throw new Exception (“Un expected value // “ + memberType.ToString() + “’.”)
// If we introduce a new user type in //future, we can easily find
// the problem here.
}
```

**Not**

```objc
if ( memberType == eMemberTypes.registered )
{
// Registered user… do something…
}
else
{
// Guest user... do something…
// If we introduce another user type in // future, this code will
// fail and will not be noticed.
}
```

Avoid using member variables. Declare local variables wherever necessary and pass it to other methods instead of sharing a member variable between methods. If you share a member variable between methods, it will be difficult to track which method changed the value and when.

Never hardcode a path or drive name in code. Get the application path programmatically and use relative path.

Avoid having very large files. If a single file has more than 1000 lines of code, it is a good candidate for refactoring. Split them logically into two or more classes.

Avoid public methods and properties, unless they really need to be accessed from outside the class. Use “internal” if they are accessed only within the same assembly.

Avoid passing too many parameters to a method. If you have more than 4~5 parameters, it is a good candidate to define a class.

If you have a method returning a collection, return an empty collection instead of null, if you have no data to return. For example, if you have a method returning an ArrayList, always return a valid `arrayList`. If you have no items to return, then return a valid `arrayList` with 0 items. This will make it easy for the calling application to just check for the `arrayList.count` rather than doing an additional check for `nil`.

# Other Objective-C Style Guides

If ours doesn't fit your tastes, have a look at some other style guides:

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)