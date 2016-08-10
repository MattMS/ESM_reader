# Parse file data

## Library imports

	R = require 'ramda'

	{always, both, cond, converge, F, identity, ifElse, juxt, lte, pipe, prop, propEq, T, unfold, where} = R


## Relative imports

These functions are the different types of Buffer processors that can be called.
The appropriate one is chosen by the `single_pass` switching function.

	parse_field = require './field'

	start_new_group = require './start_new_group'

	start_new_record = require './start_new_record'


## Pick parser

Decide which function should be called to process the Buffer.

	passed_group_stop = converge(lte, [prop('group_stop_byte'), prop('stop_byte')])

	passed_record_stop = converge(lte, [prop('record_stop_byte'), prop('stop_byte')])

	pick_parser = cond([
		[passed_group_stop, start_new_group]
		[passed_record_stop, start_new_record]
		[T, parse_field]
	])


## Main loop

	main_loop = unfold(
		pipe(
			pick_parser,
			ifElse(propEq('bytes', 0), F, juxt([identity, identity]))
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
