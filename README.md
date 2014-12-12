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
* [Proprocessor Directives](#proprocessor-directives)
* [Declarations](#declarations)
* [Expressions](#expressions)
* [Control Structures](#control-structures)
* [Exceptions and Error Handling](#exceptions-and-error-handling)
* [Expressions](#expressions)
* [Blocks](#blocks)
* [Literals](#literals)
* [Categories](#categories)
* [Class Headers](#class-headers)
* [Reactive Cocoa](#reactive-cocoa)
* [Objective-clean](#objective-clean)

## Whitespace

* Indentation with **4 spaces**, **no Tabs**.
* Liberal use of whitespace within methods to divide code into logical chunks.
* Method braces open on the **next line** from method declaration. `if`\\`else`\\`switch`\\`while` statement braces open on the **same line**.
* There is no requirement to limit code line screen length. Xcode word wrapping does a pretty good job. However this shouldn't be abused and code must be divided into sepparate lines given that it would improve readability. *Most cases will be outlined in other sections of this document*.

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

* before and after these statments: @implementation, @interface, @class, @protocol, #pragma mark  
* before and after the method or function implementation
* before @end    

<!-- TZ: TODO: add more instances and examples -->

## Proprocessor Directives

* Use #pragma mark - <section_name> to sepparate class code into sections of similar function.
* Use #warning to indicate incomplete or malfunctioning code.
* TODO's should be written as comments. Adding [this script](https://coderwall.com/p/bfd0dq) to the project build phases will display all TODO's as warnings
* Use #ifdef, #ifndef and #define to change constants and flow based on what configuration the project is built on, by passing preprocessor macros.

### For example:

```objc
#pragma mark - Dummy Initiation
- (id)init
{
    #warning this doesn't work
    return nil;
}
    
- (id)initWithFoo:(id)bar
{
#ifdef DEBUG
    return 1;
#endif
    return nil;
    //TODO: Add more spice.
}
```
    
## Declarations

* Don’t use line breaks in method declarations.
* Method declarations should begin with (`-`/`+`) symbol followed by a space and the return type. Properties should only have a space separating them from each other and a the `*` symbol from the property type (where applicable):
```objc
- (void)foo:(id)bar something:(NSObject *)something;
```
* Always declare memory-management semantics even on `readonly` properties.
* Declare properties `readonly` if they are only set once in `-init`.
* Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
* Ivar's should be prefixed with an underscore (just like when implicitly synthesized).
* Don't put a space between an object type and the protocol it conforms to.
```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;
```
* Constructors should generally return instancetype rather than id.

## Expressions

* Don't access an ivar unless you're in -init, -dealloc or a custom accessor.
* Use the dot notation when invoking idempotent methods (their return values don't change much). This includes property getters, setters and class methods like `NSFileManager.defaultManager`.
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

##Exceptions and Error Handling

* Don't use exceptions for flow control.
* Use exceptions only to indicate programmer error.
* To indicate errors, use an NSError ** argument.

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
    
##Categories

* Categories should be named for the sort of functionality they provide.
* Expossing private methods for subclasses or unit testing should be done by creating a class extension named `Class_Private.h`.
* Massive view controllers or huge manager singletons can have their code dispersed and categorized over several files with categories. In these cases we leave the category method declaration calls in the main class header, but we create different files for category method impementations.

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

##Class Headers

Inspiration for this came from using some mature 3rd party library [PSPDFKit](http://pspdfkit.com) and reading [Structuring Modern Objective-C](http://ashfurrow.com/blog/structuring-modern-objective-c) article.

* Don't #import in Header Files Unnecessarily. Here are the [Benefits](http://qualitycoding.org/file-dependencies/)

####Class.h
```objc
class MyOtherClass;

@interface MyClass : NSObject

@property (nonatomic, strong) MyOtherClass property;

@end
```

##Reactive Cocoa

For [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) and other frameworks the rely heavily of block chaining we enforce the indenting of every chained RACSignal/RACStream operation:

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
