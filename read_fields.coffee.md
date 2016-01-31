# Get field data

## Library imports

	R = require 'ramda'

	ramped = require 'ramped'


## Relative imports

	buffer_to = require './buffer_to'

	calculate_stop_byte = require './calculate_stop_byte'


## Helper functions

	calculate_then_apply = (args, call)->
		R.pipe R.juxt(args), R.apply(call)

	is_at_least = R.lte

	has_name_and_size = R.pipe R.length, is_at_least(6)

	get_field_data_size = buffer_to.int16 4

	get_field_name = R.pipe R.slice(0, 4), buffer_to.ascii

	get_data_buffer = calculate_then_apply [R.always(0), get_field_data_size, R.drop(6)], R.slice

	get_field_size = R.pipe get_field_data_size, R.add(6)

	# rest_of_buffer = calculate_then_apply [get_field_size, R.identity], R.drop

	start_field_from_buffer = R.converge R.merge, [
		R.pipe R.prop('buffer'), ramped.make_object
			length: get_field_size
			name: get_field_name
			value: get_data_buffer
	,
		ramped.make_object start_byte: R.prop 'last_byte'
	]

	calculate_field_value = R.curry (call, field)->
		object_from_values = R.converge(R.objOf, [R.prop('name'), R.prop('value')])

		evolve_value = R.pipe object_from_values, R.evolve(call), R.values, R.nth(0)

		field = ramped.evolve_with_input(value: evolve_value) field

		# if call[field.name]?
		# 	field = R.clone field
		# 	field.value = call[field.name] field.value

		field

	unfolder = (call)->
		field_modifier = calculate_field_value call

		(buffer_view)->
			if not has_name_and_size buffer_view.buffer
				false

			else
				get_field = R.pipe start_field_from_buffer, calculate_stop_byte, field_modifier

				field = get_field buffer_view

				buffer_view =
					buffer: buffer_view.buffer.slice field.length
					last_byte: field.stop_byte

				[field, buffer_view]


## Exports

	module.exports = R.curry (call, input)->
		get_all_fields = R.unfold unfolder call

		fields = get_all_fields input

		if call.after?
			fields = call.after fields

		fields
