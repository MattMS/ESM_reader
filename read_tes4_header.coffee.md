# Read TES4 header

## File structure

- 4 ASCII bytes: `TES4`

- 20 bytes: unknown


## Library imports

	R = require 'ramda'

	ramped = require 'ramped'


## Relative imports

	buffer_to = require './buffer_to'

	calculate_stop_byte = require './calculate_stop_byte'


## Helper functions

	get_main_values = R.pipe R.prop('buffer'), ramped.make_object
		name: buffer_to.label 0
		length: R.always 24

	merge_start_byte = R.converge R.merge, [
		get_main_values
		ramped.make_object start_byte: R.prop 'last_byte'
	]


## Exports

	module.exports = R.pipe merge_start_byte, calculate_stop_byte
