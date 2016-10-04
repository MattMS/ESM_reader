# TES4

## Structure

### HEDR

- `HEDR`

- 2: Always `0x0c00` (length = 12).

- 4, float: version number (`0.94` in Fallout 3).

- 4, integer: record count.

- 4, integer: next object ID.


### CNAM

- `CNAM`

- 2: length of next field.

- text (<512) + `0x00`: Author name.


### MAST

There may be multiple references to master files.

- `MAST`

- 2: length of next field.

- text (<512) + `0x00`: Master file name.

- `DATA`

- 2

- 8


## Library imports

	{applySpec, compose, invoker} = require 'ramda'


## Relative imports

	buffer_to = require '../../buffer_to/main'


## Helper functions

	float_to_fixed = invoker(1, 'toFixed')


## Exports

	module.exports =
		CNAM: buffer_to.ascii

		HEDR: applySpec
			version: compose(float_to_fixed(2), buffer_to.float(0))

			record_count: buffer_to.uint32(4)

			next_object_id: buffer_to.uint32(8)

		MAST: buffer_to.ascii

		#ONAM_: (value)->


## Links

- [TES4 4 @ UESP](http://www.uesp.net/wiki/Tes4Mod:Mod_File_Format/TES4)

- [TES4 5 @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/TES4)
