# Read file data

## Library imports

	bunyan = require 'bunyan'

	R = require 'ramda'

	{always, append, apply, assoc, both, call, cond, converge, dec, dropLast, flip, identity, ifElse, inc, invoker, isArrayLike, isNil, juxt, last, lensPath, lensProp, lte, mergeAll, objOf, over, pick, pipe, prop, propEq, props, sum, T, tap, view, where} = R


## Relative imports

	add_stop_byte = require './add_stop_byte'

	read_field = require './read_field'

	read_group_header = require './read_group_header'

	read_record_header = require './read_record_header'


## Record handler imports

	handlers =
		ASPC: require './records/aspc'
		DOOR: require './records/door'
		FACT: require './records/fact'
		GLOB: require './records/glob'
		GMST: require './records/gmst'
		MGEF: require './records/mgef'
		SCPT: require './records/scpt'
		SOUN: require './records/soun'
		TES4: require './records/tes4'
		TXST: require './records/txst'


## Logging

	log = bunyan.createLogger
		name: 'read_file_data'
		serializers:
			state: pick([
				'buffer'
				'group_number'
				'group_stop_byte'
				'last_byte'
				'record_number'
				'record_stop_byte'
			])


## Helper functions

	append_over = R.curry (lens, func, obj)->
		over(lens, append(func(obj)), obj)

	# ensure_array = R.unless(isArrayLike, R.of)

	get_last_data_from_state = pipe(prop('data'), last)


## Buffer parsers

These functions are the different types of Buffer processors that can be called.
The appropriate one is chosen by the `single_pass` switching function.

They are defined in the order that they will be found in a file (group > record > fields).


### Common parsing functions

These provide common functionality used by each of the parsers.

	calculate_stop_byte = pipe(
		get_last_data_from_state,
		props(['data_bytes', 'stop_byte']),
		sum
	)

	dec_group_number = over(lensProp('group_number'), dec)

	drop_from_buffer = invoker(1, 'slice')

	inc_group_number = over(lensProp('group_number'), inc)

	last_record_is_empty = pipe(get_last_data_from_state, propEq('bytes', 0))

	trim_buffer_bytes = converge(assoc, [
		always('buffer'),
		pipe(
			juxt([
				pipe(get_last_data_from_state, prop('bytes')),
				prop('buffer')
			]),
			apply(drop_from_buffer)
		),
		identity
	])


### Start group

	set_group_label = converge(assoc, [
		always('group_label'),
		pipe(get_last_data_from_state, prop('label')),
		identity
	])

	set_group_stop_byte = converge(assoc, [
		always('group_stop_byte'),
		calculate_stop_byte,
		identity
	])

	start_new_group = pipe(
		inc_group_number,
		append_over(
			lensProp('data'),
			pipe(
				juxt([
					pipe(prop('buffer'), read_group_header),
					pipe(prop('last_byte'), objOf('start_byte')),
					pick(['group_number']),
					always(record_number: 1)
				]),
				mergeAll,
				add_stop_byte
			)
		),
		ifElse(last_record_is_empty, dec_group_number, pipe(
			trim_buffer_bytes,
			set_group_label,
			set_group_stop_byte
		))
	)


### Start record

	inc_record_number = over(lensProp('record_number'), inc)

	set_record_name = converge(assoc, [
		always('record_name'),
		pipe(get_last_data_from_state, prop('name')),
		identity
	])

	set_record_stop_byte = converge(assoc, [
		always('record_stop_byte'),
		calculate_stop_byte,
		identity
	])

	start_new_record = pipe(
		append_over(
			lensProp('data'),
			pipe(
				juxt([
					pipe(prop('buffer'), read_record_header),
					pipe(prop('last_byte'), objOf('start_byte')),
					pick(['group_number', 'record_number']),
				]),
				mergeAll,
				add_stop_byte
			),
		),
		R.unless(last_record_is_empty, pipe(
			trim_buffer_bytes,
			inc_record_number,
			set_record_name,
			set_record_stop_byte
		))
	)


### TES4 record (file start)

	set_group_stop_from_record_stop = converge(assoc, [
		always('group_stop_byte'),
		prop('record_stop_byte'),
		identity
	])

	parse_TES4_record = pipe(
		inc_group_number,
		start_new_record,
		ifElse(last_record_is_empty, dec_group_number, set_group_stop_from_record_stop)
	)


### Read field data

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

	update_field_value = converge(
		over(lensProp('value')),
		[
			get_value_handler,
			identity
		]
	)

	make_field_object_from_state = pipe(
		juxt([
			pipe(prop('buffer'), read_field),
			pipe(prop('last_byte'), objOf('start_byte')),
			pick(['group_label', 'group_number', 'record_name', 'record_number']),
		]),
		mergeAll,
		update_field_value,
		add_stop_byte
	)

	read_field_from_state = pipe(
		append_over(
			lensProp('data'),
			make_field_object_from_state
		),
		trim_buffer_bytes
	)


## Pick parser

Decide which function should be called to process the Buffer.

	passed_group_stop = converge(lte, [prop('group_stop_byte'), prop('last_byte')])

	passed_record_stop = converge(lte, [prop('record_stop_byte'), prop('last_byte')])

	pick_parser = cond([
		[
			passed_group_stop,
			ifElse(
				propEq('group_number', 0),
				parse_TES4_record,
				start_new_group
			)
		]
		[passed_record_stop, start_new_record]
		[T, read_field_from_state]
	])

	remove_last_record = over(lensProp('data'), dropLast(1))

	set_last_byte_from_data = converge(assoc, [
		always('last_byte'),
		pipe(get_last_data_from_state, prop('stop_byte')),
		identity
	])

	single_pass = pipe(
		pick_parser,
		ifElse(last_record_is_empty, remove_last_record, set_last_byte_from_data)
	)


## Main loop

TODO: Maybe create an `unfold` function here for looping the Buffer.

	main_loop = (state)->
		for i in [1,2,3]
			state = single_pass(state)

		state


## Verify input

Make sure that the object passed in has the properties required for parsing.

	input_passes_spec = where
		buffer: R.is(Buffer)
		data: isArrayLike
		group_label: R.is(String)
		group_number: R.is(Number)
		group_stop_byte: R.is(Number)
		last_byte: R.is(Number)
		record_number: R.is(Number)
		record_stop_byte: R.is(Number)

	verify_input = both(R.is(Object), input_passes_spec)


## Exports

	module.exports = ifElse(verify_input, main_loop, always({}))

	# module.exports.log = log
