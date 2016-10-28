# SimpleMap
A `matlab` handle class for persistent storage of arbitrary data. Provides a mapping from a single subscript to arbitrary data.

## Usage

### Create an empty map
```
>> smObj = SimpleMap
smObj = 
SimpleMap containing 0 stored values, max index of 0.
```

### Assign some data
```
>> smObj(1) = [1 2 3]; 
>> smObj(2) = 'a';
>> smObj(4) = {4 5 6}
smObj = 
SimpleMap containing 3 stored values, max index of 4.
```

### Extract the data
```
>> a = smObj(1);
>> b = smObj(3)
b =
     []
```

### Find out which indices are used in the map
```
>> vbIndices = spy(smObj)
vbIndices =
     1     1     0     1

>> find(smObj)
ans =
     1     2     4
```

### `SimpleMaps` are a handle class
```
>> smObj = SimpleMap;	% Make a new empty SimpleMap
>> smObj2 = smObj;		% Copy the SimpleMap object
>> smObj(1) = 'Map 1';	% Assign data to the first map
>> smObj2(1)				% The data is also present in the copy
ans =
Map 1
```
The benefit of `SimpleMap` being a `handle` class, is to enable persistent data store within objects that are not themselves `handle` classes.

### Empty the map
```
>> empty(smObj);
>> smObj
smObj = 
SimpleMap containing 0 stored values, max index of 0.
```

## Using a `SimpleMap` for persistent data storage within a value class
```
classdef ValueClass
	properties (Hidden)
		smPersistent = SimpleMap;
	end
	
	methods
		function fResult = LongCalculation(vcObj, nIndex)
			% - Check whether the result has already been calculated
			if (~isempty(vcObj.smPersistent(nIndex)))
				fResult = vcObj.smPersistent(nIndex);
			else
				% - No, so compute the result from scratch
				fResult = PerformLongCalculation(nIndex);
				
				% - Cache the result
				vcObj.smPersistent(nIndex) = fResult;
			end
		end
	end
end
```
The property `smPersistent` will maintain data assigned to it from within methods of `ValueClass`, even though it is not a handle class. This is useful for internal hidden caching of results, not as useful for user-facing properties.

In the example above, a `SimpleMap` is used for "memoization" of the result of a long calculation. The result is cached, and this cache is maintained even though the object `vcObj` is not returned by the `LongCalculation` method.

This is most useful if the semantics of a class do not lend themselves to being a handle class.