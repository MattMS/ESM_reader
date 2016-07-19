# Read group header

## File structure

- 4 ASCII bytes: `GRUP`

- 4 byte unsigned integer: size of group (including the 24 byte header).

- 4 ASCII bytes: label

- 4 byte unsigned integer

- 2 byte unsigned integer: last edit day of month

- 2 byte unsigned integer: last edit month since December 2002

- 6 bytes


### Label type

| Value | Bytes | Type               | Description                          |
| ----- | ----- | ------------------ | ------------------------------------ |
|     0 |     4 | ASCII              | Record type                          |
|     1 |     4 | unsigned integer   | World (WRLD) children                |
|     2 |     4 | integer            | Interior cell block number           |
|     3 |     4 | integer            | Interior cell sub-block number       |
|     4 |     4 | 2 x 2 byte integer | Exterior cell block grid Y, X        |
|     5 |     4 | 2 x 2 byte integer | Exterior cell sub-block grid Y, X    |
|     6 |     4 | unsigned integer   | Cell (CELL) children                 |
|     7 |     4 | unsigned integer   | Topic (DIAL) children                |
|     8 |     4 | unsigned integer   | Persistent cell (CELL) childen       |
|     9 |     4 | unsigned integer   | Temporary cell (CELL) children       |
|    10 |     4 | unsigned integer   | Visible distant cell (CELL) children |

- Note the reverse order of the exterior cell integers.


## Library imports

	R = require 'ramda'

	{assocPath, always, applySpec, converge, identity, ifElse, invoker, lte, path, pipe, prop, propSatisfies} = R


## Relative imports

	buffer_to = require './buffer_to'


## Helper functions

	get_date_since_2002_12 = (months, day_of_month)->
		last_edit = new Date 2002, 12, 1

		# invoker('setMonth')(prop('last_edit_month_since_2002_12'))
		last_edit.setMonth months

		last_edit.setDate day_of_month + 1

		last_edit.toISOString().slice(0, 10)

	calculate_last_edit_date = converge(assocPath(['value', 'last_edit_date']), [
		converge(get_date_since_2002_12, [
			path(['value', 'last_edit_month_since_2002_12']),
			path(['value', 'last_edit_day_of_month'])
		]),
		identity
	])

	get_main_values = applySpec
		bytes: always 24
		name: buffer_to.label 0
		value:
			data_bytes: buffer_to.uint32 4
			label: buffer_to.label 8
			# label_top: buffer_to.label 0
			label_type: buffer_to.uint32 12
			last_edit_day_of_month: buffer_to.uint8 16
			last_edit_month_since_2002_12: buffer_to.uint8 17
			# unknown_1: buffer_to.uint16 18
			# unknown_2: buffer_to.uint16 22
			version: buffer_to.uint16 20

	has_byte_length = propSatisfies(lte(24), 'length')


## Exports

	module.exports = ifElse(has_byte_length, pipe(get_main_values, calculate_last_edit_date), always({bytes: 0}))


## Links

- [Groups File Format @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Groups)
