Objective-C Style
=======================

Subjects are arranged in the order you'd find each in an average Objective-C code file conforming to these styles.

## Table of Contents

* [Descriptions](#descriptions)
* [Importing and Forward-Declaring](#importing-and-forward-declaring)
* [Macros](#macros)
* [Comments](#comments)
* [Variables](#variables)
 	* [Variable Naming](#variable-naming)
	* [Static Variables](#static-variables)
	* [Instance Variables](#instance-variables)
	* [Properties](#properties)
	* [Private Properties](#private-properties)
* [Methods](#methods)
	* [Method Naming](#method-naming)
	* [Method Syntax](#method-syntax) 
	* [Init](#init)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Literals](#literals)
* [Types](#types)
 	* [CGRect](#cgrect)
	* [Enumerated Types](#enumerated-types)
	* [Bitmasks](#bitmasks)

## Descriptions

Every code file in an Objective-C project, as most others, should be unique and important in-and-of itself. As such, each deserves a brief, succinct description and disclaimer. This block of metadata, which is almost entirely standard in Xcode, should prefix every ```.h``` and ```.m``` file:

	//
	//  UICollectionView.h
	//  UIKit
	//	Manages an ordered collection of data items and presents them using customizable layouts.
	//	
	//  Created by Apple on 04/08/95.
	//  Copyright (c) 2011-2013, Apple Inc. All rights reserved.
	//


## Importing and Forward-Declaring

Every implementation file should have its own header, as each header file should have its own forward-declarations and imports. The use of Objective-C one-line forward declarations, signified by ```@class``` above the ```@interface``` of a header file, paired with ```#import``` in the implementation, [is by far the most ideal](http://nshipster.com/at-compiler-directives/). All referenced classes should be individually and explicitly dealt with on a per-file basis, to ensure long-time operability and easy workspace sharing. Even if this means including ```#import <UIKit/UIKit.h>``` in the beginning of every ```UIView``` subclass created.

Including extensive forward-declarations in ```.h``` files also visually connects the implementations with their required classes, preventing unintentional harm by altering those classes without realizing the usage.

All import statements should appear at the start of the file, with no preceding lines or whitespace (excluding the [file description](#descriptions)). It is recommended to group imports by framework or directory, for example:

	#import <UIKit/UIKit.h>
	#import <QuartzCore/QuartzCore.h>
	
	#import "Reachability/Reachability.h"
	
	#import "JWState.h"
	
	@class JWWorld, JWContinent, JWCountry;
	
	@interface JWCity : JWState
	
## Macros

Macros are very useful, simple tools to use when debugging or quickly changing small values inline. As such, they should be used lightly, obviously, and with non-critical matters (such as aesthetic properties). The standard camelCase and class prefixing found in standard variable names should not be employed with macros, which are simply copy-and-paste notes for the compiler. In addition, macros should be shared between files when relevant, to reduce clutter and conflicts. The optimal location for a ```#define``` is following all [imports and forward-declarations](#importing-and-forward-declaring), preceding static variables and the interface itself. Here's a great example of a useful tweak macro:


	#ifdef DEBUG
		#define DEBUG_LOG(fmt, ...) NSLog((@"[Project] %s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
	#else
		#define DEBUG_LOG(fmt, ...) 
	#endif

Avoid using macros for object types and constant key values, as well as even remotely complex functions. Static variables and C-functions are always preferable.


## Comments

However verbose we make our Objective-C code, there are always situations where a little more explaining could go a long way. Leaving small, inline comments directly beside a relatively short line of code is the tidiest way to go.

	NSInteger radius = ceilf(powf(2.0 * M_PI * r, 2.0)); // Rounded up to prevent 1/x pixel display issues
	
This style preserves readability, without interrupting the flow of the code too much, and forces the comment to be succint. In the case of long, branching ```if``` / ```else``` statements, consider refactoring the code to split most components into small, more modular functions. Then, about a set of conditions:

	// If the user's credentials weren't found
	if (. . .)
	
	// If the user's credentials couldn't be secured
	else if (. . .)
	
	// If the user's credentials were incorrect
	else

The most valuable and documentative form of comments comes in the form of Apple-doc conforming property and method header blocks. These are incredibly useful, particularly if kept up-to-date with recent code, for both readability and idea mulling-over. Here's a great, extended example:

	/**
 	*  Synchronously deploy a warning shot to the given cities.
 	*
 	*  @param locations Array of arrays; every element is another array, with the first element being the name of the state, and the following elements names of cities in that state.
 	*
 	*  @return Dictionary with cites : respose. Both conform to NSString.
 	*
 	*  @see deployWarningShotToWorld A more streamlined way of getting your point across.
	*  @warning Paste me inline to warn about the dangers of poking bears.
 	*/
	- (NSDictionary *)deployWarningShotToLocations:(NSArray *)locations;


More examples can be found in this handy [NSHipster article](http://nshipster.com/documentation/). Make sure to pick up [VVDocumenter-Xcode](https://github.com/onevcat/VVDocumenter-Xcode) for speedy comment block creation.

## Variables

### Variable Naming

Always choose lengthy, descriptive, attractive names for variables. Especially when creating static variables, instance variables, or properties, context needs to be embedded in the name— sacrificing minor typing time (use autocomplete already!) for a huge increase in understandability.

	@property(nonatomic, retain) CGFloat initialBallAngularVelocity;
	
This also spares the need for attached comments, and lets anyone understand the basic structure of the code. When creating local variables in a specific method, however, some specificity can be forgone:

	- (NSAttributedString *)refreshControlAttributedStringFromDate:(NSDate *)date {
		NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
		NSString *prefixedDescription = . . .

which is more suitable (and less sanity-taxing) than:

	- (NSAttributedString *)refreshControlAttributedStringFromDate:(NSDate *)date {
		NSDateFormatter *refreshControlDateFormatter = [[NSDateFormatter alloc] init];
		NSString *refreshControlPrefixedDescription = . . .
		
		
### Static Variables

Static variables representing constant, shared values should use [Hungarian Notation](http://stackoverflow.com/questions/500030/what-is-the-significance-of-starting-constants-with-k) in the header file (directly proceeding ```@interface```):

	static NSString * kContentReadyToBeStreamedNotificationName = @"Content.Stream.Ready"

where inline static variables can remain unaltered from standard variable camelCase:

	+ (JWPreferencesManager *)sharedManager {
		static dispatch_once_t dispatchToken;
		static JWPreferencesManager *sharedManager = nil;
 		
 		dispatch_once(&dispatchToken, ^{
 			sharedManager = [[self alloc] init];
 		});
 		
		return sharedManager;
	}

([Singletons: You're doing them wrong](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html) conformed to style guides)

### Instance Variables

Instance variables which are not properties should still be represented in the header file, and should stylize themselves descriptively and without use of excessive verbage. 

	@interface StormOfSwords : ASongOfIceAndFire {
		@private
		NSString *cachedReekIdentity;
		NSInteger deathsThatShouldHaveBeenPrevented;
		@public
		CGFloat daysUntilWinterIsHere;
		@protected
		GRMStark *theKingInTheNorth;
	}
	
However, avoid using them over [private properties](#private-properties) unless explicitly required.

### Properties

Properties and their names should be treated with the same care and descriptiveness as method names. Avoid including properties that have overlapping content, or are unused outside of the class. Always prefix unique properties with [appropriate comments](#comments).

	/** . . .
	@property(nonatomic, readwrite) BOOL active;
	
	/** . . .
	@property(nonatomic, retain) NSArray *quickReplyToolbarTitles;

When accessing properties in the implementation, always choose to use underscore notation over dot notation or synthesizing. If you wish to refer specifically to the property's getter or setter, use ```[self active]``` over ```self.active``` (which are [neccessarily equivalent](http://stackoverflow.com/questions/10333495/difference-beetween-and-self-in-objective-c)).

	_active = NO;
	_quickReplyToolbarTitles = @[@"Reply", @"Ignore", @"Read"];
	
This makes the origin of the variables transparent, and forces local method usage to conform to the same styles as external method usage.
	
	[self myOwnGetter] ≡ [friend hisOwnGetter] 
	_myOwnProperty ≡ friend.hisOwnProperty
	
### Private Properties

Whenever a property doesn't need to be accessed externally (and may benefit from hidden usage, such as ```UIViews``` in a complex level of abstraction), always utilize private properties over instance variables.

In the ```.m```:
	
	@interface JWCompositeView ()
	
	@property (strong, nonatomic, readonly) FXBlurView *blurBackingView;
	
	@end
	
## Methods

### Method Naming

Methods should be named in the traditional Objective-C, verbose manner, using sensical parameter names and nearly-English phrasing.

	- (void)presentAttachmentViewControllerAbove:(UIViewController *)currentViewController

### Method Syntax

Only use bracket notation when accessing methods, and dot notation when accessing properties; prefer ```array.count``` over ```[array count]```, and don't be afraid to let your IDE wrap lines with long method names.

	[self addTranslationSpringWithMass:5.0 stiffness:1.0 damping:0.25 startTime:1.0 velocity:2.0 timing:springTiming];


### Init

Always create obvious initializations for classes with many required properties, and always include all overriden -init methods in headers (even ```- (instancetype)init```). The basic form of an Objective-C constructor (accessible in Xcode via snippets) is ideal:

	- (instancetype)initWithFrame:(CGRect)frame {
		self = [super initWithFrame:frame];
		
		if (self) {
			self.backgroundColor = [UIColor redColor];
		}
		
		return self;
	}

## Spacing

Always indent based on context. In general, use tabs over spaces for all aesthetic padding. Block braces should always be preceded by a space, even inline:

	[UIView animateWithDuration:0.4 animations:^(void){
		[confirmationPulse setTransform:CGAffineTransformMakeScale(25.0, 25.0)];
			confirmationPulse.alpha = 0.0;
	} completion:^(BOOL finished) {
		[confirmationPulse removeFromSuperview];
	}];


Use whitespaces lines gratuitously, at least one before and after each method implementation, and ```#pragma mark```.

Include padding whitespace when using ternary operators: 

	NSString *validString = givenString && givenString.length > 0 ? givenString : @"N/A";
	
	
## Conditionals 

As described in the [NYTimes Style Guide](https://github.com/NYTimes/objective-c-style-guide):

> Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

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

As described in the [NYTimes Style Guide](https://github.com/NYTimes/objective-c-style-guide):

> The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**

	result = a > b ? x : y;

**Not:**

	result = a > b ? x = c > d ? c : d : y;
	
## Literals

Always follow [Modern Objective-C Guidelines](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html) when utilizing `NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals.

	NSString *friendWithWindowsPhone = @"Ben";
	NSArray *friendsWithoutConfirmation = @[@"Noah", @"Chandler", @"Morgan"];
	NSDictionary *friendsOperatingSystems = @{ @"Jake" : @"Android", @"Will" : @"iOS 7", @"Dean" : @"iOS 8" };
	NSNumber *friendsWithoutSmartphones = @(2);
	

## Types

### CGRect

As described in the [NYTimes Style Guide](https://github.com/NYTimes/objective-c-style-guide):

> When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

>> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

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

### Enumerated Types

As described in the [NYTimes Style Guide](https://github.com/NYTimes/objective-c-style-guide):


When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

	typedef NS_ENUM(NSInteger, NYTAdRequestState) {
	    NYTAdRequestStateInactive,
	    NYTAdRequestStateLoading
	};

### Bitmasks

As described in the [NYTimes Style Guide](https://github.com/NYTimes/objective-c-style-guide):

When working with bitmasks, use the `NS_OPTIONS` macro.

	typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
	  NYTAdCategoryAutos      = 1 << 0,
	  NYTAdCategoryJobs       = 1 << 1,
	  NYTAdCategoryRealState  = 1 << 2,
	  NYTAdCategoryTechnology = 1 << 3
	};
