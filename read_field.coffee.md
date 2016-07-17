# Read field

## Library imports

	{add, always, applySpec, both, converge, identity, ifElse, invoker, length, lte, pipe, slice} = require 'ramda'


## Relative imports

	buffer_to = require './buffer_to'


## Helper functions

	get_data_size = buffer_to.int16(4)

	get_field_name = pipe(slice(0, 4), buffer_to.ascii)

	get_full_field_size = pipe(get_data_size, add(6))

	has_name_and_size = pipe length, lte(6)

	slice_from_buffer = invoker(2, 'slice')


## Main functions

	get_main_values = applySpec
		bytes: get_full_field_size
		name: get_field_name
		value: converge(slice_from_buffer, [always(6), get_full_field_size, identity])


First ensure that there are 6 bytes (to get the size bytes) then calculate and check the full size.

	has_byte_length = both(
		pipe(length, lte(6)),
		converge(lte, [get_full_field_size, length]),
	)


## Exports

	module.exports = ifElse(has_byte_length, get_main_values, always({}))
