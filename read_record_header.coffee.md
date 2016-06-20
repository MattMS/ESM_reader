# Read record header

## File structure

| Bytes | Type             | Description                         |
| ----- | ---------------- | ----------------------------------- |
|     4 | ASCII            | Label (usually matches group label) |
|     4 | unsigned integer | Size of record                      |
|     4 | -                | Bit flags                           |
|     4 | unsigned integer | Record ID                           |
|     4 | unsigned integer | Revision ID                         |
|     2 | unsigned integer | Version                             |
|     2 | -                | -                                   |


### Links

- [Records @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Records)


## Library imports

	R = require 'ramda'

	ramped = require 'ramped'


## Relative imports

	buffer_to = require './buffer_to'

	calculate_stop_byte = require './calculate_stop_byte'


## Helper functions

	get_data_size = buffer_to.uint32 4

Plain version of `label` is `buffer.slice(0, 4).toString('ascii')`.

	get_main_values = R.pipe R.prop('buffer'), ramped.make_object
		#flags = buffer.slice 8, 12

		bytes: get_data_size
		id: buffer_to.uint32 12
		label: R.compose buffer_to.ascii, R.slice(0, 4)
		# length: R.compose R.add(24), get_data_size
		length: R.always 24
		revision: buffer_to.uint32 16
		unknown: buffer_to.uint16 22
		version: buffer_to.uint16 20

	get_values_with_start = R.converge R.merge, [
		get_main_values
		ramped.make_object start_byte: R.prop 'last_byte'
	]


## Exports

	module.exports = R.pipe get_values_with_start, calculate_stop_byte
