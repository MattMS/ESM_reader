# GRUP (group)

## File structure

- 4 ASCII bytes: `GRUP`

- 4 byte unsigned integer: size of group (including the 24 byte header).

- 4 ASCII bytes: label

- 4 byte unsigned integer

- 2 byte unsigned integer: last edit day of month

- 2 byte unsigned integer: last edit month since December 2002

- 6 bytes


### Links

- [Groups File Format @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Groups)


## Library imports

	R = require 'ramda'

	ramped = require 'ramped'


## Relative imports

	buffer_to = require './buffer_to'

	calculate_stop_byte = require './calculate_stop_byte'


## Helper functions

	calculate_last_edit_date = ramped.evolve_with_input
		last_edit_date: (value)->
			get_date_since_2002_12 value.last_edit_month_since_2002_12, value.last_edit_day_of_month

	get_date_since_2002_12 = (months, day_of_month)->
		last_edit = new Date 2002, 12, 1

		last_edit.setMonth months

		last_edit.setDate day_of_month + 1

		last_edit.toISOString().slice(0, 10)

	get_main_values = R.pipe R.prop('buffer'), ramped.make_object
		bytes: buffer_to.uint32 4
		label: buffer_to.label 8
		# label_top: buffer_to.label 0
		label_type: buffer_to.uint32 12
		last_edit_day_of_month: buffer_to.uint8 16
		last_edit_month_since_2002_12: buffer_to.uint8 17
		length: R.always 24
		# length: R.compose R.add(24), get_data_size
		# unknown_1: buffer_to.uint16 18
		# unknown_2: buffer_to.uint16 22
		version: buffer_to.uint16 20

	get_values_with_start = R.converge R.merge, [
		get_main_values
		ramped.make_object start_byte: R.prop 'last_byte'
	]


## Exports

	module.exports = R.pipe get_values_with_start, calculate_stop_byte, calculate_last_edit_date
