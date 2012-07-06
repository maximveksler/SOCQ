#SOCQ (Syntax for Objective-C Queries)

Bringin' some query love to Objective-C

Feel free to open issues(feature requests), fork, and/or open pull requests!

(MIT license, details at the bottom of this file or in the MIT-License.txt file.)

__NSArray__

- skip:
- take:
- skip:take:
- where:
- any:
- all:
- groupby:
- distinctObjectsByAddress
- distinct
- select:
- selectKeypaths:
- firstObject
- secondObject

__NSMutableArray__

- popObjectAtIndex:
- popFirstObject
- popLastObject

__NSDictionary__

- where:
- any:
- all:

__NSSet__

- where:
- any:
- all:
- groupby:
- select:
- selectKeypaths:

##NSArray
####take:
```objc
- (NSArray*)take:(NSUInteger)inCount;
```	
_Returns an array with the specified number of elements from the beginning of the target array._

```objc
// Example - Getting the first five elements
	
NSArray* elements = [people take:5];
```

####skip:
```objc
- (NSArray*)skip:(NSUInteger)inCount;
```

_Skips the indicated number of elements in the array and returns an array of the remaining elements._

```objc
// Example - skips the first five elements

NSArray* remaining = [people skip:5];
```

####skip:take:
```objc
- (NSArray*)skip:(NSUInteger)inSkip take:(NSUInteger)inTake;
```

_Simple convenience method that combines the skip and take methods. Ideal for pagination._
	
```objc
// Example - get elements 6-10
	
NSArray* remaining = [people skip:5 take:5];
```	
	
####where:
```objc
- (NSArray*)where:(BOOL(^)(id obj))check;
```

_Uses the `check` block on every element in the array to determine if they should be returned in the return array_

```objc
// Example - find people that are 25 years old
	
NSArray* 25YearOlds = [people where:^(id obj){ return [obj age] == 25; }];
```

####any:
```objc
- (BOOL)any:(BOOL(^)(id obj))check;
```

_Checks every element in the array to see if any of the elements successfully pass the `check` block. If none pass, return `NO`, else `YES`._

```objc
// Example - check to see if anyone is under 18

BOOL containsMinors = [people any:^(id obj){ return [obj age] < 18; }];
```

####all:
```objc
- (BOOL)all:(BOOL(^)(id obj))check;
```

_Checks every element in the array to see if all of the elements successfully pass the `check` block. If all elements pass, return `YES`, else `NO`._

```objc
// Example - check to see if everyone is 25 or over

BOOL everyone25orOver = [people all:^(id obj){ return [obj age] >= 25; }];
```

####groupby:
```objc
- (NSDictionary*)groupBy:(id(^)(id obj))groupBlock;
```
_Uses the object returned from the `groupBlock` block as a key to group the object into a NSDictionary that contains a NSArray with all the objects that returned the same key_

```objc
// Example - group everyone by their last name

NSDictionary* peopleByFamilyName = [people groupBy:^(id obj){ return [obj lastName]; }];
```
####distinctObjectsByAddress
```objc
- (NSArray*)distinctObjectsByAddress;
```
_Does a simple pointer address compare to remove elements that refer to the same object_

```objc
// Example - remove the exact same elements

NSArray* uniquePeople = [people distinctObjectsByAddress];
```
####distinct
```objc
- (NSArray*)distinct;
```
_Uses the class' compare and hash method to remove elements that contain the same value_

```objc
// Example - remove the exact same elements

NSArray* uniquePeople = [people distinct];
```
####select:
```objc
- (NSArray*)select:(id(^)(id originalObject))transform;
```	
_Transforms elements in the array into another strongly type object that is returned from the `transform` block._

```objc
// Example - Change people objects into American Class objects

NSArray* americans = [people select:^(id obj){ return [[American alloc] initWithFirstName:[obj firstName]
										 LastName:[obj lastName]
										      age:[obj age]] }];
```
####selectKeypaths:
```objc
- (NSArray*)selectKeypaths:(NSString*)keypath, ... NS_REQUIRES_NIL_TERMINATION;
```
_Selects properties from the elements in the array using the keypath mechanism. Any number of keypaths maybe specified but the list must be `nil` terminated. The return value is an array of dictionaries. The dictionary contain the keypaths that were passed in as the parameters as the keys and the valueForKeyPath: as the values._

```objc
// Example - Get the four properties we need from the person object

NSArray* americans = [people selectKeypaths:@"firstName",@"lastName",@"parent.firstName",@"age",nil];
```
####firstObject
```objc
- (id)firstObject;
```    
_Returns the first object in the array. If the array is empty, returns nil._

```objc
// Example - Get the first person
    
id person = [people firstObject];
```
####secondObject
```objc
- (id)secondObject;
```    
_Returns the second object in the array. If the array doesn't contain two objects, returns nil._

```objc
// Example - Get the second person
    
id person = [people secondObject];
```

##NSMutableArray
####popObjectAtIndex:
```objc
- (id)popObjectAtIndex:(NSUInteger)inIndex;
```
####popFirstObject
```objc
- (id)popFirstObject;
```
####popLastObject
```objc
- (id)popLastObject;
```

_Removes and returns the object from the array. If index is outside out the range of the array, `NSRangeException` is raised._

```objc
// Example - remove the first person from the array
Person* firstPerson = [people popFirstObject];

// Example - remove the second person from the array
Person* secondPerson = [people popObjectAtIndex:1];

// Example - remove the last person from the array
Person* lastPerson = [people popLastObject];
```
    
##NSDictionary
####where:
```objc
- (NSDictionary*)where:(BOOL(^)(id key, id value))check;
```	
_Uses the `check` block on every key-object in the dictionary to determine if they should be returned in the return array_

```objc
// Example - get all the keys and objects where the key is 3 or less characters

NSDictionary* entriesWithKeysOf3OrLess = [peopleGroup where:^(id key, id value){ return [key length] <= 3; }];
```
####any:
```objc
- (BOOL)any:(BOOL(^)(id key, id value))check;
```	
_Checks every key-object in the dictionary to see if any of the elements successfully pass the `check` block. If none pass, return `NO`, else `YES`._

```objc
// Exmaple - finds out if any of the keys are longer than 10 characters

BOOL areAnyKeysLongerThan10 = [peopleGroups any:^(id key, id value){ return [key length] > 10 }];
```
####all:
```objc
- (BOOL)all:(BOOL(^)(id key, id value))check;
```
_Checks every key-object in the dictionary to see if all of the elements successfully pass the `check` block. If all elements pass, return `YES`, else `NO`._

```objc
// Exmaple - find out if all the keys are strings

BOOL areKeysStrings = [peopleGroups all:^(id key, id value){ return [key class] == [NSString class] }];
```
##NSSet
####where:
```objc
- (NSSet*)where:(BOOL(^)(id obj))check;
```	
_Uses the `check` block on every element in the set to determine if they should be returned in the return set_

```objc
// Example - find people that are 25 years old

NSSet* 25YearOlds = [people where:^(id obj){ return [obj age] == 25; }];
```
####any:
```objc
- (BOOL)any:(BOOL(^)(id obj))check;
```
_Checks every element in the set to see if any of the elements successfully pass the `check` block. If none pass, return `NO`, else `YES`._

```objc
// Example - check to see if anyone is under 18

BOOL containsMinors = [people any:^(id obj){ return [obj age] < 18; }];
```
####all:
```objc
- (BOOL)all:(BOOL(^)(id obj))check;
```
_Checks every element in the set to see if all of the elements successfully pass the `check` block. If all elements pass, return `YES`, else `NO`._

```objc
// Example - check to see if everyone is 25 or over

BOOL everyone25orOver = [people all:^(id obj){ return [obj age] >= 25; }];
```

####groupby:
```objc
- (NSDictionary*)groupBy:(id(^)(id obj))groupBlock;
```
_Uses the object returned from the `groupBlock` block as a key to group the object into a NSDictionary that contains a NSSet with all the objects that returned the same key_

```objc
// Example - group everyone by their last name

NSDictionary* peopleByFamilyName = [people groupBy:^(id obj){ return [obj lastName]; }];
```    
####select:
```objc
- (NSSet*)select:(id(^)(id originalObject))transform;
```	
_Transforms elements in the set into another strongly type object that is returned from the `transform` block._

```objc
// Example - Change people objects into American Class objects

NSSet* americans = [people select:^(id obj){ return [[American alloc] initWithFirstName:[obj firstName]
									       LastName:[obj lastName]
										    age:[obj age]] }];
```                                                    
####selectKeypaths:
```objc
- (NSSet*)selectKeypaths:(NSString*)keypath, ... NS_REQUIRES_NIL_TERMINATION;
```
_Selects properties from the elements in the set using the keypath mechanism. Any number of keypaths maybe specified but the list must be `nil` terminated. The return value is an set of dictionaries. The dictionary contain the keypaths that were passed in as the parameters as the keys and the valueForKeyPath: as the values._

```objc
// Example - Get the four properties we need from the person object

NSSet* americans = [people selectKeypaths:@"firstName",@"lastName",@"parent.firstName",@"age",nil];
```
------

Created by Adam Burkepile on 5/2/12.

Copyright (C) 2012 Adam Burkepile.

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

