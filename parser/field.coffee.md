# Parse field data

## Library imports

	R = require 'ramda'

	{always, converge, flip, identity, ifElse, inc, isNil, juxt, lensPath, lensProp, mergeAll, objOf, over, pipe, prepend, prop, view} = R


## Relative imports

	add_stop_byte = require '../add_stop_byte/main'

	get_common_props = require './get_common_props'

	handlers = require '../records/all'

	inc_field_count = require '../inc_field_count/main'

	read_field = require '../read_field/main'

	set_field_number = require '../set_field_number/main'

	trim_buffer_bytes = require '../trim_buffer_bytes/main'


## Get value handler

Give the state object for the current field, return the function to convert the raw field data to a useful form.

	get_value_handler = pipe(
		pipe(
			juxt([
				prop('group_label'),
				prop('name')
			])
			lensPath,
			flip(view)(handlers)
		),
		R.when(isNil, always(identity))
	)


## Update field value

	update_field_value = converge(
		over(lensProp('value')),
		[
			get_value_handler,
			identity
		]
	)


## Exports

	module.exports = pipe(
		juxt([
			get_common_props,
			pipe(prop('buffer'), read_field),
			pipe(prop('stop_byte'), objOf('start_byte')),
		]),
		mergeAll,
		update_field_value,
		add_stop_byte,
		inc_field_count,
		set_field_number,
		trim_buffer_bytes
	)
