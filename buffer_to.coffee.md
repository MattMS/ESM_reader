# Convert a Buffer to other values

## Imports

	R = require 'ramda'


## Helper functions

	buffer_to_string = R.invoker 1, 'toString'

	last_is_0 = R.pipe R.last, R.equals 0

	strip_last_0 = R.when last_is_0, R.slice(0, -1)

	buffer_to_ascii = R.pipe strip_last_0, buffer_to_string 'ascii'

	swap_slashes = R.replace /\\/g, '/'


## Exports

	module.exports =
		ascii: buffer_to_ascii

		#char: R.compose String.fromCharCode, R.nth
		char: (index)->
			(buffer)->
				String.fromCharCode R.nth index, buffer

		file_path: R.pipe buffer_to_ascii, swap_slashes

		float: R.invoker 1, 'readFloatLE'

		int16: R.invoker 1, 'readInt16LE'

		uint8: R.invoker 1, 'readUInt8'

		uint16: R.invoker 1, 'readUInt16LE'

		uint32: R.invoker 1, 'readUInt32LE'
