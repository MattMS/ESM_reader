# Start new record

## Library imports

	R = require 'ramda'

	{always, applySpec, assoc, converge, identity, ifElse, inc, juxt, lensProp, mergeAll, objOf, over, pipe, prop, propEq, subtract} = R


## Relative imports

	add_stop_byte = require '../add_stop_byte/main'

	calculate_stop_byte = require '../calculate_stop_byte/main'

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

	get_start_byte_from_stop_byte = pipe(prop('stop_byte'), objOf('start_byte'))

	no_data_was_parsed = propEq('bytes', 0)


## Make empty record

	make_empty_record = applySpec
		buffer: prop('buffer')
		bytes: always(24)
		record_number: always(0)
		name: prop('group_name')
		value:
			data_bytes: converge(subtract, [prop('group_stop_byte'), prop('stop_byte')])
			# flags: always(0)
			id: always(0)
			revision: always(0)
			unknown: always(0)
			version: always(0)


## Check for record-free group

	# group_has_no_record = propSatisfies(flip(contains)(['CLDC', 'HAIR', 'PWAT', 'RGDL', 'SCOL', 'SCPT']), 'group_name')
	group_has_no_record = propEq('group_name', 'SCOL')


## Exports

	module.exports = pipe(
		juxt([
			get_common_props,
			ifElse(
				group_has_no_record,
				make_empty_record,
				pipe(prop('buffer'), read_record_header)
			)
			get_start_byte_from_stop_byte,
			always({field_counts: {}})
		]),
		mergeAll,
		R.unless(no_data_was_parsed, pipe(
			R.unless(group_has_no_record, pipe(
				add_stop_byte,
				trim_buffer_bytes
			)),
			inc_record_number,
			set_record_name,
			set_record_stop_byte
		))
	)
