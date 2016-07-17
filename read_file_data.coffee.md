# Read file data

## Library imports

	bunyan = require 'bunyan'

	R = require 'ramda'

	{always, append, apply, assoc, call, cond, converge, flip, identity, ifElse, inc, invoker, isNil, juxt, last, lensPath, lensProp, lte, mergeAll, objOf, over, pick, pipe, prop, propEq, props, sum, T, tap, view} = R


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

	drop_from_buffer = invoker(1, 'slice')

	# ensure_array = R.unless(isArrayLike, R.of)

	# get_handler_by_last_group_name = pipe(prop('group_label'), flip(prop)(handlers))

	get_last_data_from_state = pipe(prop('data'), last)

	inc_group_number = over(lensProp('group_number'), inc)

	inc_record_number = over(lensProp('record_number'), inc)

	passed_group_stop = pipe(juxt([prop('group_stop_byte'), prop('last_byte')]), apply(lte))

	passed_record_stop = pipe(juxt([prop('record_stop_byte'), prop('last_byte')]), apply(lte))

	set_group_stop_from_record_stop = pipe(
		juxt([
			always('group_stop_byte'),
			prop('record_stop_byte'),
			identity
		]),
		apply(assoc)
	)


### Extended helper functions

These depend on functions above.

	calculate_stop_byte = pipe(
		get_last_data_from_state,
		props(['data_bytes', 'stop_byte']),
		sum
	)

	get_value_handler = pipe(
		pipe(
			juxt([
				prop('group_label'),
				# pipe(get_last_data_from_state, prop('name'))
				prop('name')
			])
			lensPath,
			flip(view)(handlers)
		),
		R.when(isNil, always(identity))
	)

	set_group_label = pipe(
		juxt([
			always('group_label'),
			pipe(get_last_data_from_state, prop('label')),
			identity
		]),
		apply(assoc)
	)

	set_group_stop_byte = pipe(
		juxt([
			always('group_stop_byte'),
			calculate_stop_byte,
			identity
		]),
		apply(assoc)
	)

	set_last_byte_from_data = pipe(
		juxt([
			always('last_byte'),
			pipe(get_last_data_from_state, prop('stop_byte')),
			identity
		]),
		apply(assoc)
	)

	set_record_name = pipe(
		juxt([
			always('record_name'),
			pipe(get_last_data_from_state, prop('name')),
			identity
		]),
		apply(assoc)
	)

	set_record_stop_byte = pipe(
		juxt([
			always('record_stop_byte'),
			calculate_stop_byte,
			identity
		]),
		apply(assoc)
	)

	trim_buffer_bytes = pipe(
		juxt([
			always('buffer'),
			pipe(
				juxt([
					pipe(get_last_data_from_state, prop('bytes')),
					prop('buffer')
				]),
				apply(drop_from_buffer)
			),
			identity
		]),
		apply(assoc)
	)

	update_field_value = pipe(
		juxt([
			get_value_handler,
			pipe(get_last_data_from_state, prop('value')),
		]),
		apply(call),
		call
	)


## Main functions

These functions are the different types of Buffer processors that can be called.
The appropriate one is chosen by the `single_pass` switching function.

	read_field_from_state = pipe(
		append_over(
			lensProp('data'),
			pipe(
				juxt([
					pipe(prop('buffer'), read_field),
					pipe(prop('last_byte'), objOf('start_byte')),
					pick(['group_label', 'group_number', 'record_name', 'record_number']),
				]),
				mergeAll,
				# tap((d)-> console.log d),
				converge(
					over(lensProp('value')),
					[
						get_value_handler,
						identity
					]
				),
				add_stop_byte
			)
		),
		trim_buffer_bytes
	)


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
		trim_buffer_bytes,
		set_group_label,
		set_group_stop_byte
	)


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
		trim_buffer_bytes,
		inc_record_number,
		set_record_name,
		set_record_stop_byte
	)


## Single pass

Decide which function should be called to process the Buffer.

	pick_parser = cond([
		[
			passed_group_stop,
			ifElse(
				propEq('group_number', 0),
				pipe(
					inc_group_number,
					start_new_record,
					# set_group_label,
					set_group_stop_from_record_stop
				),
				start_new_group
			)
		]
		[passed_record_stop, start_new_record]
		[T, read_field_from_state]
	])

	single_pass = pipe(pick_parser, set_last_byte_from_data)


## Exports

TODO: Maybe create an `unfold` function here for looping the Buffer.

	module.exports = (state)->
		for i in [1,2,3]
			state = single_pass(state)

		state

	# module.exports.log = log