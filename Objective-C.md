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
	* [Variable Syntax](#variable-syntax)
	* [CGRect](#cgrect)
	* [Enumerated Types](#enumerated-types)
* [Methods](#methods)
	* [Method Naming](#method-naming)
	* [Method Syntax](#method-syntax) 
	* [Init](#init)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Literals](#literals)

## Descriptions

Every code file in an Objective-C project, as most others, should be unique and important in-and-of itself. As such, each deserves a brief, succinct description and disclaimer. This block of metadata, which is almost entirely standard in Xcode, should prefix every ```.h``` and ```.m``` file:

	//
	//  Class.(h or m)
	//  Project
	//  Description
	//
	//  Created by Author on mm/dd/yy.
	//  Copyright (c) yyyy Author. All rights reserved.
	//


## Importing and Forward-Declaring

Every implementation file should have its own header, as each header file should have its own forward-declarations and imports. The use of Objective-C one-line forward declarations, signified by ```@class``` in the beginning of a header file, paired with ```#import``` in the implementation, [is by far the most ideal](http://nshipster.com/at-compiler-directives/). All referenced classes should be individually and explicitly dealt with on a per-file basis, to ensure long-time operability and easy workspace sharing. Even if this means including ```#import <UIKit/UIKit.h>``` in the beginning of every ```UIView``` subclass created.
