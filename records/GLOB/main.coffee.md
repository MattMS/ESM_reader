# GLOB (global variable)

Contains information on Global Variables.


## Structure

| Bytes | Type             | Description          |
| ----- | ---------------- | -------------------- |
|     4 | ASCII            | Always `EDID`        |
|     2 | unsigned integer | Size of next field   |
|     * | ASCII            | ASCII text then `\0` |
|     4 | ASCII            | Always `FNAM`        |
|     2 | unsigned integer | Always 1             |
|     1 | unsigned integer | Type of value        |
|     4 | ASCII            | Always `FLTV`        |
|     2 | unsigned integer | Always 4             |
|     4 | float            | value                |

- [GLOB @ USEP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/GLOB)


### FNAM value

These are the 3 possible values of the `FNAM` field.

`f`/`66`
: float

`l`/`6C`
: long

`s`/`73`
: short


### FLTV value

All global values are stored as floats.


## Imports

	buffer_to = require '../../buffer_to/main'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		FLTV: buffer_to.float 0

		FNAM: buffer_to.char 0
