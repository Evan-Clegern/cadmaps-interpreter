# CADMAPS Function Tokens
Bash-like enclosing. CADMAPS operations/types are all capitalized. CADMAPS uses explicit variable types.


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
