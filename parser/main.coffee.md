# Parser

## Library imports

	R = require 'ramda'

	{always, both, cond, converge, F, identity, ifElse, juxt, lensProp, lte, over, pick, pipe, prop, propEq, T, unfold, where} = R


## Relative imports

These functions are the different types of Buffer processors that can be called.
The appropriate one is chosen by the `single_pass` switching function.

	add_type = require './add_type'

	parse_field = require './field'

	pipe_logs = require '../pipe_logs/main'

	start_log = require '../start_log/main'

	start_new_group = require './start_new_group'

	start_new_record = require './start_new_record'


## Logging

	pick_parser_log = start_log identity
	# pick_parser_log = start_log over(
	# 	lensProp('value'),
	# 	pick([
	# 		'group_name'
	# 		'group_number'
	# 		'stop_byte'
	# 	])
	# )

	log = start_log identity

	pipe_logs [
		# add_type.log
		# parse_field.log
		pick_parser_log
		# start_new_group.log
		# start_new_record.log
	], log


## Last record

	no_data_was_parsed = propEq('bytes', 0)


## Pick parser

Decide which function should be called to process the Buffer.

	passed_group_stop = converge(lte, [prop('group_stop_byte'), prop('stop_byte')])

	passed_record_stop = converge(lte, [prop('record_stop_byte'), prop('stop_byte')])

NOTE: A `type` property is added in the [start_new_group](./start_new_group.coffee.md) module.

	pick_parser = cond([
		[passed_group_stop, start_new_group]
		[passed_record_stop, pipe(start_new_record, R.unless(no_data_was_parsed, add_type('record_header')))]
		[T, pipe(parse_field, R.unless(no_data_was_parsed, add_type('field')))]
	])


## Main loop

	main_loop = unfold(
		pipe(
			pick_parser,
			pick_parser_log.trace('Parser unfold'),
			ifElse(no_data_was_parsed, F, juxt([identity, identity]))
		)
	)


## Verify input

Make sure that the object passed in has the properties required for parsing.

	input_passes_spec = where
		buffer: R.is(Buffer)
		# field_counts: R.is(Object)
		# field_number: R.is(Number)
		group_label: R.is(String)
		group_number: R.is(Number)
		group_stop_byte: R.is(Number)
		record_number: R.is(Number)
		record_stop_byte: R.is(Number)
		stop_byte: R.is(Number)

	verify_input = both(R.is(Object), input_passes_spec)


## Exports

	module.exports = ifElse(verify_input, main_loop, always([]))

	module.exports.log = log
