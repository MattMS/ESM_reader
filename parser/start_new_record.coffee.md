# Start new record

## Library imports

	R = require 'ramda'

	{always, assoc, converge, identity, inc, juxt, lensProp, mergeAll, objOf, over, pipe, prop, propEq} = R


## Relative imports

	add_stop_byte = require '../add_stop_byte'

	calculate_stop_byte = require './calculate_stop_byte'

	get_common_props = require './get_common_props'

	read_record_header = require '../read_record_header'

	trim_buffer_bytes = require './trim_buffer_bytes'


## Adjust record number

	inc_record_number = over(lensProp('record_number'), inc)


## Set record details

	set_record_name = converge(assoc, [
		always('record_name'),
		prop('name'),
		identity
	])

	set_record_stop_byte = converge(assoc, [
		always('record_stop_byte'),
		calculate_stop_byte,
		identity
	])


## Last record

	last_record_is_empty = propEq('bytes', 0)


## Exports

	module.exports = pipe(
		pipe(
			juxt([
				get_common_props,
				pipe(prop('buffer'), read_record_header),
				pipe(prop('stop_byte'), objOf('start_byte')),
			]),
			mergeAll,
			add_stop_byte
		),
		R.unless(last_record_is_empty, pipe(
			trim_buffer_bytes,
			inc_record_number,
			set_record_name,
			set_record_stop_byte
		))
	)
