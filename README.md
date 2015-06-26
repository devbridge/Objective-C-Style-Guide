![Devbridge Group](https://raw.githubusercontent.com/devbridge/Objective-C-Style-Guide/master/devb-logo.png)
# Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at [Devbridge Group](http://www.devbridge.com). This style guide is always updated and accepts change requests. Shoot us a mail at **mobile@devbridge.com**.

## Introduction

Motivated by the coding patterns we adopted over the years and inspired by other great style guides like the [NYTimes](https://github.com/NYTimes/objective-c-style-guide), [GitHub](https://github.com/github/objective-c-conventions) and [Google's](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml). If something isn't mentioned in this document it was probably covered in-depth by Apple:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Whitespace](#whitespace)
* [Expressions](#expressions)
* [Control Structures](#control-structures)
* [Expressions](#expressions)
* [Blocks](#blocks)
* [Literals](#literals)
* [Exceptions and Error Handling](#exceptions-and-error-handling)
* [Preprocessor Directives](#proprocessor-directives)
* [Comments](#comments)
* [Imports](#imports)
* [Declarations](#declarations)
* [IBOutlets](#iboutlets)
* [Categories](#categories)
* [Protocols](#protocols)
* [Tests](#tests)
* [ReactiveCocoa](#reactivecocoa)
* [Objective-clean](#objective-clean)

## Whitespace

* Indentation with **4 spaces**, **no Tabs**.
* Liberal use of whitespace within methods to divide code into logical chunks.
* There is no requirement to limit code line screen length. Xcode word wrapping does a pretty good job. However, this shouldn't be abused and code must be divided into sepparate lines given that it would improve readability. *Most cases will be outlined in other sections of this document*.

### For example:

```objc
- (void)foo
{
    if (something) {
        return;
    } 
    else {
        return;
    }
}
```

###New lines

Instances when adding a new line is required:

* before and after these statments: `@implementation`, `@interface`, `@class`, `@protocol`, `#pragma mark`
* before and after the method or function implementation
* before `@end`
* Method braces open on the **next line** from method declaration. `if`\\`else`\\`switch`\\`while` statement braces open on the **same line**.

<!-- TZ: TODO: add more instances and examples -->

## Expressions

* Don't access an instance variable unless you're in `-init`, `-dealloc` or a custom accessor.
* Use the dot notation when invoking idempotent methods (their return values don't change much). This includes property getters and setters as well as methods that act like getters.
* `BOOL` comparisons should never be explicit. `if (isSomething == YES) {}` is not guaranteed to work because a true `BOOL` can be any bit value but not a 0. Use `if (isSomething) {}` instead.
* Prefer positive comparisons to negative.
* Avoid long ternary operators, when nesting more than one is require use nested `if` statements instead.
From:
```objc
int a = (stuff == thing ? (test ? foo : barbar) : bar);
```
To:
```objc
int a;
if (stuff == thing) {
    if (test) {
       a = foo;
    } 
    else {
       a = barbar;
    }
} 
else {
    a = bar;
}
```

* Use short form, `nil` coalescing ternary operators where applicable.
```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

* Separate binary operands with a single space, but unary operands and casts with none:
```objc
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```

## Control Structures
* Always surround if bodies with curly braces if there is an else. Single-line if bodies without an else should be on the same line as the if.
* All curly braces should begin on the same line as their associated statement. They should end on a new line.
* Put a single space after keywords and before their parentheses.
* Return and break early.

### For example:
```objc
if (somethingIsBad) return;
    
if (something == nil) {
    // do stuff
} 
else {
    // do other stuff
}
```

##Blocks

* Blocks should have a space between their return type and name.
* Block definitions should omit their return type when possible.
* Block definitions should omit their arguments if they are `void`.
* Parameters in block types should be named unless the block is initialized immediately.

###For example:
```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^(id args) {
    // do some things
};
```

##Literals

* Avoid making numbers a specific type unless necessary (for example, prefer `5` to `5.0`, and `5.3` to `5.3f`).
* Elements of array and dictionary literals should have a space after their separating comma.
* The contents of array and dictionary literals should have no spaces on sides.
* Dictionary literals should have a space between the colon and the value.
* Long and complex literals are to be split over multiple lines, with lines having a terminating comma where appropriate.

###For example:
```objc
NSArray *stuff = @[@1, @2, @3];
NSDictionary *keyedStuff = @{GHDidCreateStyleGuide: @YES};
    
NSArray *stuff = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];
    
NSDictionary *keyedStuff = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff"
};
```

##Exceptions and Error Handling

* Don't use exceptions for flow control.
* Use exceptions only to indicate programmer error.
* Use `NSParameterAssert` in methods that have required parameters. Do not put expressions that change values inside these macros.
* To indicate errors, use an `NSError **` argument.

   
## Preprocessor Directives

* Use `#pragma mark - <section_name>` to sepparate class code into sections of similar function. In particular, when implementing delegate's methods, name the section after delegates name. For instance, methods of `UITextFieldDelegate` should be located under `#pragma mark - UITextFieldDelegate` section.
* Use `#ifdef`, `#ifndef` and `#define` to change constants and flow based on what configuration the project is built on, by passing preprocessor macros.
* Note, try avoiding uses of `#define` for constants. Consider using static and extern variables instead. It helps to have strongly referenced code that is better managed by Xcode. See [this article](http://qualitycoding.org/preprocessor/) for more details.
* Avoid using `#warning` or `#error` directives, unless it is really neeeded. Having artificial warnings obscure the view of real problems. If you want to annotate your code, consider using [comments](#comments) instead.

### For example:

```objc

#pragma mark - My section

- (id)init
{
    return nil;
}
    
- (id)initWithFoo:(id)bar
{
#ifdef DEBUG
    return 1;
#endif
    return nil;
}
```

## Comments

Consider using Doxygen-style documentation in your header files. These comments are automatically hooked up by Xcode to produce on-demand documentation similar to system classes. This snippet can be saved to Xcode to quickly generate comments on shortcut:

```
/**
 <#description#>
 @discussion <#discussion#>
 @param <#parameter#>
 @returns <#retval#>
 @exception <#throws#>
 */
```

Use following comment structures to keep track of issues in code. Xcode's jumpbar will automatically highlight these comments. In addition, using [XToDo](https://github.com/trawor/XToDo) plugin will make it easier to keep track of them.

* Use `TODO` to indicate incomplete code.
* Use `FIXME` to indicate malfunctioning code.
* Use `!!!` for code that is likely to have a critical issue.
* Use `???` for unclear code that needs to be discussed.

### For example:

```objc
//TODO: Implement this

//FIXME: This is buggy

//!!!: This is totally wrong!

//???: Why is it here?
```

## Imports

* When importing system frameworks or Cocoapods dependencies, use angle brackes and put a framework name as a prefix:
```objc
#import <UIKit/UIKit.h>
#import <AFNetowkring/AFNetowkring.h>
```
* Imports have to be included in headers, where protocol definition is needed.
* Imports have to be moved out of header files, where possible. If your header file contains a property or a method that requires specific class, define that class with `@class` instruction and put `#import` into implementation file instead. See [this article](http://qualitycoding.org/file-dependencies/) for more details.
* Eliminate unused imports in your classes. Remember, the less dependencies a class has, the easier it is to test it.
* Never use `#include`, unless it is C++ code.
* Consider using `@import` over `#import` in projects that support Objective-C modules. See [this article](https://stoneofarc.wordpress.com/2013/06/25/introduction-to-objective-c-modules/) for details.

### For example:

####MyClass.h:
```objc
#import "MyOtherClass.h"

@class MyYetAnotherClass;
@interface MyClass <MyOtherClassDelegate>

@property (nonatomic, strong) MyYetAnotherClass *myProperty;

@end
```

####MyClass.m:
```objc
#import "MyClass.h"
#import "MyYetAnotherClass.h"

@implementation MyClass

@end
```
    
## Declarations

* Don’t use line breaks in method declarations.
* Method declarations should begin with (`-`/`+`) symbol followed by a space and the return type. Properties should only have a space separating them from each other and a the `*` symbol from the property type (where applicable):
```objc
- (void)foo:(id)bar something:(NSObject *)something;
```
* Always declare memory-management semantics even on `readonly` properties.
* Declare properties `readonly` if they are only set once in `-init`.
* Delegates must be marked as `weak`.
* Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
* Instance variables should be prefixed with an underscore (just like when implicitly synthesized).
* Don't put a space between an object type and the protocol it conforms to.
```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;
```
* Constructors should generally return `instancetype` rather than `id`.
* Singleton methods must return `instancetype`.
* Consider using `NS_DESIGNATED_INITIALIZER` for `-init` methods that must be used to create an instance:
```objc
- (instancetype)initWithName:(NSString *)name NS_DESIGNATED_INITIALIZER;
```
* Method declarations that accept NSError pointers must embed `__autoreleasing` attribute. In addition, such methods must return `BOOL`, according to conventions:
```objc
- (BOOL)parseResponse:(id)response error:(NSError *__autoreleasing *)error;
```
* Properties that are used solely inside a class must be defined in class extension inside the implementation file and not in the header file.
* Instance variables must be defined in class extension inside the implementation file and not in the header file.
 
##IBOutlets

* Always place IBOutlets inside implementation file.
* If you want to expose IBOutlet property to outside world, consider creating a shadow readonly property in the header file, while still keeping IBOutlet inside the implementation file.

###For example:
####Class.h
```objc
@interface Class
    
@property (nonatomic, readonly) UILabel *myLabel;
    
@end
```

####Class.m
```objc
#import "Class.h"
    
@interface Class ()

@property (nonatomic, strong) IBOutlet UILabel *myLabel;

@end
    
@implementation Class

@end
```

##Categories

* Categories should be named for the sort of functionality they provide. 
* Category name and class extension's brackets should be separated from the class name with a whitespace.
* Category filenames should be constructed as ClassName+CategoryName (with plus being an actual part of the filename).
* Expossing private methods for subclasses or unit testing should be done by creating a class extension named `Class_Private.h`.
* Massive view controllers or huge manager singletons can have their code dispersed and categorized over several files with categories. In these cases we leave the category method declaration calls in the main class header, but we create different files for category method impementations.
* Categories for classes from system frameworks or 3rd-party dependencies must be prefixed with the abreviation of the project followed by underscore to avoid runtime collisions with hidden methods. For instance:
```objc
@interface UIImage (MyCategory)

- (UIImage *)db_resizeImage;

@end
```

###For example:
####Class.h
```objc
@interface Class
    
- (id)init;
    
@end
    
@interface Class (Foo)
    
- (void)bar;
    
@end
```

####Class.m
```objc
#import "Class.h"
    
@implementation Class
    
- (id)init
{
    return nil;
}
    
@end
```
    
####Class+Foo.m
```objc
#import "Class.h"
@implementation Class (Foo)

- (void)bar
{
    return;
}

@end
```

##Protocols

* Do not define protocol methods as optional, unless it is explicitly needed.
* When calling delegate's optional methods, always check that it is implemented, for instance:
```objc
if ([self.delegate respondsToSelector:@selector(optionalMethod)]) {
	[self.delegate optionalMethod];
}
```
* Unless explicitly needed for subclassing, declare protocol support in class extension inside implementation file instead of the header file.

###For example:
####Class.h:
```objc
@interface Class

@end
```

####Class.m:
```objc
#import "Class.h"

@interface Class () <UITextFieldDelegate>

@end

@implementation Class

@end
```


##Tests

* Name test class names after the class that is being tested. For example, `MyClass_tests.m`.
* For each tests case, put underscore in the method's name after word test and elsewhere if it makes it easier to read. For instance: `- (void)test_operationSucceeds` or `- (void)test_operationSucceeds_withDelay`.
* Avoid constructing bulky test methods. Instead separate the large code block into smaller methods and name them appropriately. If they have common setup routine, consider writing a helper method or using `- setUp`.

##ReactiveCocoa

For [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) and other frameworks that rely heavily on block chaining, we enforce the indenting of every chained RACSignal/RACStream operation:

```objc
RACStream *result = [stream startWith:@0];

RACStream *result2 = [stream map:^(NSNumber *value) {
    return @(value.integerValue + 1);
}];
```

When transforming the same stream multiple times, ensure that all of the steps are aligned. Complex operators like +zip:reduce: or +combineLatest:reduce: may be split over multiple lines for readability:
```objc
[[RACSignal 
    merge:@[ 
        [client fetchUserRepos],
        [client fetchOrgRepos]
    ]] 
    subscribeCompleted:^{
        NSLog(@"They're both done!");
    }];
```

When invoking a single method upon a stream, no additional indentation is necessary (block arguments aside):
```objc
RACStream *result = [stream startWith:@0];

RACStream *result2 = [stream map:^(NSNumber *value) {
    return @(value.integerValue + 1);
}];
```

Generally we try to follow the official [ReactiveCocoa guidelines](https://github.com/ReactiveCocoa/ReactiveCocoa/blob/master/Documentation/DesignGuidelines.md#indent-stream-operations-consistently)

##Objective Clean

To enforce some of these rules automatically we use the [Objective-Clean](http://objclean.com/) app. Here's our default [StyleSettings](https://raw.githubusercontent.com/devbridge/Objective-C-Style-Guide/master/StyleSettings.plist).
