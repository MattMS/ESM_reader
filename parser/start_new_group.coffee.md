# Start new group

## Library imports

	R = require 'ramda'

	{assoc, always, converge, dec, identity, ifElse, inc, juxt, lensProp, mergeAll, objOf, over, path, pipe, prop, propEq} = R


## Relative imports

	add_stop_byte = require '../add_stop_byte/main'

	add_type = require '../add_type/main'

	calculate_stop_byte = require './calculate_stop_byte'

	get_common_props = require './get_common_props'

	read_group_header = require '../read_group_header'

	start_new_record = require './start_new_record'

	trim_buffer_bytes = require './trim_buffer_bytes'


## Adjust group number

	dec_group_number = over(lensProp('group_number'), dec)

	inc_group_number = over(lensProp('group_number'), inc)


## Set group details

	set_group_label = converge(assoc, [
		always('group_label'),
		# prop('label'),
		path(['value', 'label']),
		identity
	])

	set_group_name = converge(assoc, [
		always('group_name'),
		prop('name'),
		identity
	])

	set_group_stop_byte = converge(assoc, [
		always('group_stop_byte'),
		calculate_stop_byte,
		identity
	])


## Last record

	last_record_is_empty = propEq('bytes', 0)


## TES4 record (file start)

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


## Start group

	start_new_group = pipe(
		inc_group_number,
		pipe(
			juxt([
				get_common_props,
				pipe(prop('buffer'), read_group_header),
				pipe(prop('stop_byte'), objOf('start_byte')),
				always({field_counts: {}}),
				always({record_number: 1})
			]),
			mergeAll,
			add_stop_byte
		),
		ifElse(last_record_is_empty, dec_group_number, pipe(
			trim_buffer_bytes,
			set_group_label,
			set_group_name,
			set_group_stop_byte
		))
	)


## Exports

	module.exports = ifElse(
		propEq('group_number', 0),
		pipe(parse_TES4_record, R.unless(last_record_is_empty, add_type('record_header'))),
		pipe(start_new_group, R.unless(last_record_is_empty, add_type('group_header')))
	)
