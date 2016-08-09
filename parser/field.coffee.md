# Parse field data

## Library imports

	R = require 'ramda'

	{always, converge, flip, identity, isNil, juxt, lensPath, lensProp, mergeAll, objOf, over, pipe, prop, view} = R


## Relative imports

	add_stop_byte = require '../add_stop_byte'

	get_common_props = require './get_common_props'

	read_field = require '../read_field'

	trim_buffer_bytes = require './trim_buffer_bytes'


## Record handler imports

	handlers =
		ASPC: require '../records/aspc'
		DOOR: require '../records/door'
		FACT: require '../records/fact'
		GLOB: require '../records/glob'
		GMST: require '../records/gmst'
		MGEF: require '../records/mgef'
		SCPT: require '../records/scpt'
		SOUN: require '../records/soun'
		TES4: require '../records/tes4'
		TXST: require '../records/txst'


## Get value handler

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
		trim_buffer_bytes
	)
