# CADMAPS Definitions
CADMAPS... 
- uses Bash-like enclosing. 
- operations/types are all capitalized.
- uses explicit variable types.
- aims to be fairly readable

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
| PRECISION | `long float` | 0 to +/- 9,007,199,254,740,992; 15 decimal places |
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
| EMPTY | `void` |

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
| Name | Info |
|------|--|
| imaginary | Some square root occurred of a negative w/o explicit `UNREAL` |
| too_big | A value was passed that was larger than expected or handleable |

# Function Declaration
FUNCTION `name` TAKING `list` DOES ..
| Command | Notes |
|--|--|
| GIVE `what` | Effectively a "successful" return for `what` |
| GIVE `what` ANDWARN `warning` | Return `what` with a constructed warning |
| RAISE `error` | Stop function and raise `error` from `ERRORS` |
| WARN `warning` | Continue function but raise `warning` from `WARNINGS` |
#### Example:
```
FUNCTION sqrt TAKING {int a} DOES
	WHEN a == 0 DO
		GIVE 0 ANDWARN is_zero
	ORWHEN a < 0 DO
		RAISE imaginary
	WHENELSE DO
		GIVE (sqrt(a))
	ENDWHEN
ENDFUNCTION
```

# Class Overviews
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
FROM `set<type>` SELECT `var` KEEPDO .. *with `var` being `USHORT`*

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
| TESTFOR `type` .. | explicit exceptions matching `type`; declarative |
| CHECKFOR `type` .. | warnings matching `type`; declarative |
| ANDTESTFOR `type` .. | explicit exceptions matching `type`; fork |
| ANDCHECKFOR `type` .. | warnings matching `type`; fork |
| ANDTESTELSE `var` .. | store unhandeld exceptions into new `var`; finalizing |
| ANDCHECKELSE `var` .. | store unchecked warnings into new `var`; finalizing |
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

