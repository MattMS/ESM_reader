# GLOB (global variable)

## Structure

- `EDID`

- 2: length of next field

- variable: ascii + `\0`

- `FNAM`

- 2: length of next field

- 1: type: `f`/`66` float, `l`/`6C` long, `s`/`73` short

- `FLTV`

- 2: length of next field

- 4


## Imports

	buffer_to = require '../../buffer_to/main'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		FLTV: buffer_to.float 0

		FNAM: buffer_to.char 0
