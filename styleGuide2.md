# Degree 53 Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at Degree 53.

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
* [DLog](#dlog)
* [Pragma Marks](#pragma-marks)
* [Xcode Project](#xcode-project)

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**

    view.backgroundColor = [UIColor orangeColor];
    [UIApplication sharedApplication].delegate;


**Not:**

    [view setBackgroundColor:[UIColor orangeColor]];
    UIApplication.sharedApplication.delegate;


## Spacing

* Indent using 1 tab sapce. However make sure that the 'prefer indent using' setting is set to Spaces in Xcode text editing prefrences. Also make sure the tab width and ident width are both set to 2 spaces.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**For example:**

    if (user.isHappy) {
    //Do something
    }
    else {
    //Do something else
    }

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation if used.

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**

    if (!error) {
        return success;
    }


**Not:**

    if (!error)
        return success;

or

    if (!error) return success;


### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**

    result = a > b ? x : y;


**Not:**

    result = a > b ? x = c > d ? c : d : y;


## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**

    NSError *error;
    if (![self trySomethingWithError:&error]) {
       // Handle Error
    }


**Not:**

    NSError *error;
    [self trySomethingWithError:&error];
    if (error) {
        // Handle Error
    }


Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**For Example**:

    - (void)setExampleText:(NSString *)text image:(UIImage *)image;

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

    @interface DEGSection: NSObject

    @property (nonatomic) NSString *headline;

    @end


**Not:**

    @interface DEGSection : NSObject {
        NSString *headline;
    }


## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**For example:**

    UIButton *settingsButton;


**Not**

    UIButton *setBut;


A three letter prefix (e.g. `DEG`) should always be used for class names and constants, however may be omitted for Core Data entity names. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**For example:**

    static const NSTimeInterval DEGViewControllerNavigationFadeAnimationDuration = 0.3;

**Not:**

    static const NSTimeInterval fadetime = 1.7;


Properties should be camel-case with the leading word being lowercase. **If Xcode can automatically synthesize the variable, then let it.** Otherwise, in order to be consistent, the backing instance variables for these properties should be camel-case with the leading word being lowercase and a leading underscore. This is the same format as Xcode's default synthesis.

**For example:**

    @synthesize descriptiveVariableName = _descriptiveVariableName;

**Not:**

    id varnm;


### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. Local variables should not contain underscores.

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## init and dealloc

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` should be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

    - (instancetype)init {
        self = [super init]; // or call the designated initalizer
        if (self) {
            // Custom initialization
        }

        return self;
    }


## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**


    NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
    NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
    NSNumber *shouldUseLiterals = @YES;
    NSNumber *buildingZIPCode = @10018;

**Not:**


    NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
    NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
    NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
    NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];


## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

    CGRect frame = self.view.frame;

    CGFloat x = CGRectGetMinX(frame);
    CGFloat y = CGRectGetMinY(frame);
    CGFloat width = CGRectGetWidth(frame);
    CGFloat height = CGRectGetHeight(frame);


**Not:**

    CGRect frame = self.view.frame;

    CGFloat x = frame.origin.x;
    CGFloat y = frame.origin.y;
    CGFloat width = frame.size.width;
    CGFloat height = frame.size.height;


## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**


    static NSString * const DEGAboutViewControllerCompanyName = @"Degree 53";

    static const CGFloat DEGImageThumbnailHeight = 50.0;


**Not:**


    #define CompanyName @"Degree 53"

    #define thumbnailHeight 2


## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**


    typedef NS_ENUM(NSInteger, DEGAdRequestState) {
        DEGAdRequestStateInactive,
    	DEGAdRequestStateLoading
    };


## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `DEGPrivate` or `private`) should never be used unless extending another class.

**For example:**


    @interface DEGAdvertisement ()

    @property (nonatomic, strong) GADBannerView *googleAdView;
    @property (nonatomic, strong) ADBannerView *iAdView;
    @property (nonatomic, strong) UIWebView *adXWebView;

    @end


## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.


## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

    objc
    if (!someObject) {
    }


**Not:**


    if (someObject == nil) {
    }


-----

**For a `BOOL`, here are two examples:**


    if (isAwesome)
    if (![someObject boolValue])


**Not:**


    if ([someObject boolValue] == NO)
    if (isAwesome == YES) // Never do this.


-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:


    @property (assign, getter=isEditable) BOOL editable;

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.


    + (instancetype)sharedInstance {
        static id sharedInstance = nil;

       static dispatch_once_t onceToken;
       dispatch_once(&onceToken, ^{
          sharedInstance = [[self alloc] init];
       });

       return sharedInstance;
    }

This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## DLog

DLog should be used in place of NSLog in all situations. NSLog should not be used directly in the code.

    #if DEBUG 
    #define DLog(format, ...) NSLog((format), ## __VA_ARGS__)
    #else
    #define DLog(format, ...)
    #endif


##Pragma Marks

Pragma marks should be used to split up all protocols.

**For example:**

    #pragma mark - UITextField Delegate
    
    - (BOOL)textFieldShoudReturn:(UITextField *)textfield;

    - (void)textFieldDidEndEditing:(UITextField *)textField;

    #pragma mark - UITableView Delegate
 
    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

