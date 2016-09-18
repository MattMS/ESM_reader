# Convert a Buffer to other values

## Imports

	R = require 'ramda'

	{add, compose, converge, curry, equals, identity, invoker, last, nth, pipe, replace, slice} = R


## Helper functions

	buffer_to_string = invoker 1, 'toString'

	last_is_0 = pipe last, equals 0

	strip_last_0 = R.when last_is_0, slice(0, -1)

	buffer_to_ascii = pipe strip_last_0, buffer_to_string 'ascii'

	swap_slashes = replace /\\/g, '/'


## Exports

	module.exports =
		ascii: buffer_to_ascii

		#char: R.compose String.fromCharCode, R.nth
		char: curry (index, buffer)->
			String.fromCharCode nth index, buffer

		file_path: pipe buffer_to_ascii, swap_slashes

		float: invoker 1, 'readFloatLE'

		int16: invoker 1, 'readInt16LE'

		label: curry (index, buffer)->
			compose(buffer_to_ascii, slice(index, index + 4))(buffer)
		# label: pipe(converge(slice, [identity, add(4)]), buffer_to_ascii)
		# label: compose(buffer_to_ascii, converge(slice, [identity, add(4)]))

		uint8: invoker 1, 'readUInt8'

		uint16: invoker 1, 'readUInt16LE'

		uint32: invoker 1, 'readUInt32LE'
