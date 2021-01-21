# CADMAPS Definitions
CADMAPS... 
- uses Bash-like enclosing. 
- operations/types are all capitalized.
- uses explicit variable types.
- has `#` as its comment character.
- aims to be fairly readable.

# Base Types
### Integers
| Name | C++ Representation | Notes |
|--|--|--|
| INTEGER | `int` | 0 to +/- 2,147,483,648 |
| UINTEGER | `unsigned int` | 0 to 4,294,967,295 |
| SHORT | `short int` | 0 to +/- 32,768 |
| USHORT | `unsigned short int` | 0 to 65,535 |
| LONG | `long long int` | 0 to +/- 9,223,372,036,854,775,808 |
| ULONG | `unsigned long long int` | 0 to 18,446,744,073,709,551,615 |
#### Imaginary Types
Just accompany with `UNREAL`, like `UNREALINTEGER`
### Decimals
| Name | C++ Representation | Notes |
|--|--|--|
| DECIMAL | `float` | 0 to +/- 16,777,216; 7 decimal places |
| PRECISION | `long float` | 0 to +/- 9,007,199,254,740,992; 15 decimal places -> `double` |
### Characters
| Name | C++ Representation | Notes |
|--|--|--|
| LETTER | `char` | A signed 8b integer |
| BYTE | `unsigned char` | 0 to 255; 8b integer |
| STRING | `char[]` | A list of LETTERS makes a STRING |
### Lists
| Name | C++ Representation | Notes |
|--|--|--|
| SET<`type`> | `std::vector<(type)>` | Mutable vector of `type` |
| TSET<`type`> | `std::map<char[], (type)>` | Mutable map of `type` |
| NESTSET<`type`> | `std::vector<std::vector<(type)>>` | Mutable vector of `type` vectors |
| NESTTSET<`type`> | `std::vector<std::map<char[], (type)>>` | Mutable vector of `type` maps |
### Other
| Name | C++ Representation |
|--|--|
| TOGGLE | `bool` |
| REFERENCE | `*` |
| NEW_REFERENCE | `&` |
| EMPTY | `void` |
| NULL | `NULL` |
## Threading Types
### Integers
| Name | C++ Representation |
|--|--|
| SAFEINTEGER | `std::atomic<int>` |
| SAFEUINTEGER | `std::atomic<unsigned int>` |
| SAFESHORT | `std::atomic<short int>` |
| SAFEUSHORT | `std::atomic<unsigned short int>` |
| SAFELONG | `std::atomic<long long int>` |
| SAFEULONG | `std::atomic<unsigned long long int>` |
### Other
| Name | C++ Representation |
|--|--|
| SAFELETTER | `std::atomic<char>` |
| SAFEBYTE | `std::atomic<unsigned char>` |
| SAFETOGGLE | `std::atomic<bool>` |
| OPERLOCKER | `std::mutex` |
| OPERTHREAD | `std::thread` |
| MEMORY_RELAXED | `std::memory_order_relaxed` |
| MEMORY_GETSYNCHRO_ALL | `std::memory_order_acquire` |
| MEMORY_GETSYNCHRO_DEPEND | `std::memory_order_consume` |
| MEMORY_SETSYNCHRO_FORCE | `std::memory_order_release` | 
| MEMORY_SEQUENTIAL | `std::memory_order_seq_cst` | 
## Enumerations
| Name | Values | Notes |
|------|--------|-------|
| ENDIANNESS | `little`, `big` | For bitwise operations. See [here](https://en.wikipedia.org/wiki/Endianness). |
| BITPOSITION | `bit0`, `bit1`, `bit2`, `bit3`, `bit4`, `bit5`, `bit6`, `bit7` | For `FULLBYTE` |


## Errors
General Class has `ERRORENUM what  STRING info  UINTEGER where`

| Name | Info |
|------|--|
| imaginary | Some square root occurred of a negative w/o explicit `UNREAL`. |
| too_big | A value was passed that was larger than expected or handleable. |
| too_small | A value was passed that was smaller than expected or handleable. |
| math_overflow | Attempted to create a value too large to store (in type). |
| type_mismatch | Types do not match up at all. |
| failed_new | A `NEW` operation could not be completed. |
| failed_remove | A `REMOVE` operation could not be completed. |
| memory_unsafe | Backup prevention for a data race. |
| memory_overflow | A value has overflown in memory. |
| memory_too_full | Too much memory has been taken up. |
| memory_nullref | `REFERENCE` to something expected, got `NULL`. |
| memory_no_ref | `NEW_REFERENCE` not available for something. |
| logic_failure | Unknown failure with logic/conditions. |
| divide_zero | Attempted to divide by zero. |
| range_error | Attempted to access item out-of-range in a `SET`. |
| range_too_wide | Attempted to store item in max-size `SET`. |
| uninitialized_err | Using something that has no default value, before it's assigned one. |
| not_a_parameter | Attempted to access a param (i.e. enum item, bit) that doesn't exist. |
| not_a_function | Attempted to access a `FUNCTION` that doesn't exist. |
| no_matching_usage | An operation extension (i.e. "AS") has no matching operation. |
| no_matching_end | An operation (like `FUNCTION`) has no matching `END***`. |
| no_matching_close | An enclosed item has no matching close (like `(i > 3`). |
| no_matching_give | A `FUNCITON` without `SOMEGIVE` does not GIVE a value. |
| illegal_parameters | A `FUNCTION` is trying to take illegal parameter types. |
| illegal_call | Tried to call a `FUNCTION` with impromer parameter types. |
| c_exception | An unknown C++ Error occurred. |

## Warnings
General Class has `WARNINGENUM what  STRING info  UINTEGER where`

| Name | Info |
|--|--|
| precision_problem | An operation had a drop of precision. |
| is_zero | Function shouldn't be expected to operate on a zero. |
| type_nocast | Types can match, but a cast wasn't declared. |
| type_nodecimal | Types can match, but it's from a "decimal" to "integer." |
| reference_issue | Tried using a declared `NEW_REFERENCE` w/o `REFERENCE`. |
| custom | Enables users to make their own warning. |

# Function Declaration
- FUNCTION `name` AS `type` DOES ..
- FUNCTION `name` AS `type`, TAKING {`type paramname`,**`type paramname`,...**} DOES ..
- FUNCTION `name` AS SOMEGIVE `type` DOES ..  *`SOMEGIVE` means that "branches" that don't `GIVE` specifically `GIVE NULL`.*
- FUNCTION `name` AS SOMEGIVE `type`, TAKING {`type paramname`,**`type paramname`,...**} DOES ..

| Command | Notes |
|--|--|
| GIVE `what` | Effectively a "successful" return for `what` |
| GIVE `what` ANDWARN `warning` | Return `what` with a constructed warning |
| RAISE `error`*,`message`* | Stop function and raise `error` type. Can be accompanied with message. |
| WARN `warning`*,`message`* | Continue function but raise `warning` type. Can be accompanied with message. |

`RAISE` and `WARN` declarations automatically "generate" their error/warning with the
line number the declaration is on, and the warning text (unless specified) is the default
information about the error/warning.

#### Examples:
```
FUNCTION haha AS STRING DOES
	GIVE "haha"
ENDFUNCTION
```
and
```
FUNCTION sqrt AS SOMEGIVE DECIMAL, TAKING {int a} DOES
	WHEN a == 0 DO
		GIVE 0 ANDWARN is_zero, "0 cannot be Square Rooted"
	ORWHEN a < 0 DO
		RAISE imaginary #the SOMEGIVE path; raises error instead of GIVE. GIVEs NULL if error handled.
	WHENELSE DO
		GIVE (sqrt(a))
	ENDWHEN
ENDFUNCTION
```

# Class Overviews
## General Operations
| Name | Does... |
|--|--|
| NEW `class`(`parameters`) | Creates a new `class`, passing `parameters` to constructors. |
| REMOVE `item` | Deletes the item in question from memory. |
| COPY `item` `newitem` | Copies values in `item` to new variables as `newitem` |
| SWAP `item1` `item2` | Swaps the class values of `item1` and `item2` |

### Managed Byte - `FULLBYTE`
Provides functionality to manipulate a single `BYTE` on a binary level, along with extra functions.
| Method | Types |Function |
|--------|--|--|
| setbit(`pos`,`val`) | `pos` is `BITPOS`. `val` is `TOGGLE`. | Sets the bit at `pos` (0 to 7) to `val` (TRUE or FALSE) |
| getbit(`pos`) | Gives a `TOGGLE` | Gets the bit at `pos` (0 to 7) |
| setbyte(`inbyte`) | `inbyte` is `BYTE`. | Sets the internal `BYTE` value to `inbyte` and updates bits |
| getbyte() | Gives a `BYTE`. | Gets the internl `BYTE` value. |
| setendian(`endian`) | `endian` is `ENDIANNESS`. | Resets the ordering of bits. `BYTE` will be reset. |
| getendian() | Gives an `ENDIANNESS` | Gets the bit ordering. |

# Operations
## Comparison
| Syntax | Text Equivalent | Notes |
|--|--|--|
| `v1` ~= `v2` | `v1` CLOSETO `v2` | Within a 1/4 closeness. Strings can have 1 of 4 character difference. |
|`v1` == `v2` | `v1` SAMEAS `v2` | The classic "equals" |
| `v1` != `v2` | `v1` NOTSAMEAS `v2` | The classic "not equals" |
| `v1` > `v2` | `v1` MORETHAN `v2` | Can also apply to singular `LETTER` or `BYTE`. |
| `v1` < `v2` | `v1` LESSTHAN `v2` | Can also apply to singular `LETTER` or `BYTE`. |
| `v1` >= `v2` | `v1` MOREORSAMEAS `v2` | Ditto. |
| `v1` =< `v2` | `v1` LESSORSAMEAS `v2` | It's always bugged me how it's `<=` and not `=<` |


## Conditional Operation
| Keywords | Info |
|--|--|
| WHEN `condition` DO .. | singular declarative |
| WHEN `condition` KEEPDO .. | continuous declarative |
| ORWHEN `condition` DO .. | singular fork |
| ORWHEN `condition` KEEPDO .. | continuous fork |
| WHENELSE DO .. | singular |
| ENDWHEN | conditional close |
#### Examples:
```
WHEN i = 7 DO
	print("i like seven!")
ORWHEN i = -7 DO
	print("i also like negative seven!")
WHENELSE DO
	print("me no like")
ENDWHEN
```
and
```
WHEN i = 1 KEEPDO
	print("nerd")
ORWHEN i = 2 KEEPDO
	print("doctor")
ORWHEN i = 3 KEEPDO
	print("nevermind")
WHENELSE DO
	print("not cool")
ENDWHEN
```

## Iterative Operation
FROM `SET<type>` SELECT `var` KEEPDO .. *with `var` being `<type>`*

| Extension | Notes |
|--|--|
| WITH `index` | Allows accessible index, `index`, as a new `USHORT` |
| IF `condition` | Enables items to be tested, in order. Skips item and increments index |

| Range Iterative Operation | Notes |
|--|--|
| FROM RANGE(`to`) WITH `index` KEEPDO .. | Goes from 0 up to `to`, `index` += 1 |
| FROM RANGE(`from`,`to`) WITH `index` KEEPDO .. | Goes from `from` up to `to`, `index` += 1 |
| FROM RANGE(`from`,`to`,`step`) WITH `index` KEEPDO .. | Goes from `from` up to `to`, `index` += `step` |
| `from`, `to` and `step` being type `USHORT` | Explicit types are required |

ENDFROM is iterative close
#### Examples:
```
FROM RANGE(0, 15) WITH i KEEPDO
	print (i)
ENDFROM
```
and
```
SET<INT> bob = {...}
FROM bob SELECT item WITH i IF {item != NULL} KEEPDO
	print (i)
ENDFROM
```

## Error Handling

DURING ..

| Handling Statement | Notes |
|--|--|
| TESTFOR `type` .. | error matching `type`; declarative |
| CHECKFOR `type` .. | warning matching `type`; declarative |
| ANDTESTFOR `type` .. | error matching `type`; fork |
| ANDCHECKFOR `type` .. | warning matching `type`; fork |
| ANDTESTFOR `type` AS `var` INSTACK .. | store all errors matching `type` in new `SET<ERROR> var`; fork |
| ANDCHECKFOR `type` AS `var` INSTACK .. | store all warnings matching `type` in new `SET<WARNING> var`; fork |
| ANDTESTELSE `var` .. | store unhandeld errors into new `SET<ERROR> var`; finalizing |
| ANDCHECKELSE `var` .. | store unchecked warnings into new `SET<WARNING> var`; finalizing |
| AFTERWARDS .. | action to do if a test or check occurred; finalizing |
| ENDDURING | error handling close |

#### Example:
```
DURING
	i = (i + 4) / (i - 1)
TESTFOR divide_zero
	print("i bad")
ANDCHECKFOR precision_problem
	print("i precision bad")
ANDTESTELSE huh
	huh.print()
ANDCHECKELSE huh
	huh.print()
AFTERWARDS
	i = i * 4
ENDDURING
```

