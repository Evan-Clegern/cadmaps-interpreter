# CADMAPS Definitions
CADMAPS... 
- uses Bash-like enclosing. 
- operations/types are all capitalized.
- uses explicit variable types.
- aims to be fairly readable

# Types
### Integers
| Name | C++ Representation | Notes |
|--|--|--|
| INTEGER | `int` | 0 to +/- 2,147,483,648 |
| UINTEGER | `unsigned int` | 0 to 4,294,967,295 |
| SHORT | `short int` | 0 to +/- 32,768 |
| USHORT | `unsigned short int` | 0 to 65,535 |
| LONG | `long long int` | 0 to +/- 9,223,372,036,854,775,808 |
| ULONG | `unsigned long long int` | 0 to 18,446,744,073,709,551,615 |
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

## Thread-safe Types
### Integers
| Name | C++ Representation |
|--|--|
| SAFEINTEGER | `std::atomic<int>` |
| SAFEUINTEGER | `std::atomic<unsigned int>` |
| SAFESHORT | `std::atomic<short int>` |
| SAFEUSHORT | `std::atomic<unsigned short int>` |
| SAFELONG | `std::atomic<long long int>` |
| SAFEULONG | `std::atomic<unsigned long long int>` |

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

## Function Declaration
FUNCTION `name` TAKING `list` DOES ..
| Command | Notes |
|--|--|
| GIVE `what` | Effectively a "successful" return for `what` |
| GIVE `what` ANDWARN `warning` | Return `what` with a constructed warning |
| RAISE `error` | Stop function and raise `error` exception (an "enum") |
| WARN `warning` | Continue function but raise `warning` (an "enum") |
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
