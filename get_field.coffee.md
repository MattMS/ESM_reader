# Get field

## Imports

	buffer_to = require './buffer_to'

	R = require 'ramda'


## Helper functions

	get_field_length = buffer_to.uint16 4

	get_type_name = R.compose buffer_to.ascii, R.slice 0, 4


## Exports

	module.exports = (buffer)->
		length = get_field_length buffer

		label: get_type_name buffer

		length: 6 + length

		value: buffer.slice 6, 6 + length
