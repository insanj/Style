Objective-C Style
=======================

Subjects are arranged in the order you'd find each in an average Objective-C code file conforming to these styles.

## Table of Contents

* [Descriptions](#descriptions)
* [Importing and Forward-Declaring](#importing-and-forward-declaring)
* [Macros](#macros)
* [Comments](#comments)
* [Variables](#variables)
	* [Static Variables](#static-variables)
	* [Instance Variables](#instance-variables)
	* [Properties](#properties)
	* [Variable Naming](#variable-naming)
	* [Variable Syntax](#variable-syntax)
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

## Descriptions

Every code file in an Objective-C project, as most others, should be unique and important in-and-of itself. As such, each deserves a brief, succinct description and disclaimer. This block of metadata, which is almost entirely standard in Xcode, should prefix every ```.h``` and ```.m``` file:

	//
	//  Class.(h or m)
	//  Project
	//  Description
	//
	//  Created by Author on mm/dd/yy.
	//  Copyright (c) yyyy Maintainer. All rights reserved.
	//


## Importing and Forward-Declaring

Every implementation file should have its own header, as each header file should have its own forward-declarations and imports. The use of Objective-C one-line forward declarations, signified by ```@class``` above the ```@interface``` of a header file, paired with ```#import``` in the implementation, [is by far the most ideal](http://nshipster.com/at-compiler-directives/). All referenced classes should be individually and explicitly dealt with on a per-file basis, to ensure long-time operability and easy workspace sharing. Even if this means including ```#import <UIKit/UIKit.h>``` in the beginning of every ```UIView``` subclass created.

All import statements should appear at the start of the file, with no preceding lines or whitespace (excluding the [file description](#descriptions)). It is recommended to group imports by framework or directory, for example:

	#import <UIKit/UIKit.h>
	#import <QuartzCore/QuartzCore.h>
	
	#import "Reachability/Reachability.h"
	
	#import "JWLogger.h"
	
	@class JWWorld, JWContinent, JWCountry;
	
	@interface JWState : ...
	
## Macros

Macros are very useful, simple tools to use when debugging or quickly changing small values inline. As such, they should be used lightly, obviously, and with non-critical matters (such as aesthetic properties). The standard camelCase and class prefixing found in standard variable names should not be employed with macros, which are simply copy-and-paste notes for the compiler. In addition, macros should be shared between files when relevant, to reduce clutter and conflicts. The optimal location for a ```#define``` is following all [imports and forward-declarations](#importing-and-forward-declaring), preceding static variables and the interface itself. Here's a great example of a useful tweak macro:


	#ifdef DEBUG
		#define DEBUG_LOG(fmt, ...) NSLog((@"[Project] %s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
	#else
		#define DEBUG_LOG(fmt, ...) 
	#endif

Avoid using macros for object types and constant key values, as well as even remotely complex functions. Static variables and C-functions are always preferable.




