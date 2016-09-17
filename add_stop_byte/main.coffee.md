# Add stop byte

Given an object with `start_byte` and `bytes` values, this sets their sum to the `stop_byte` property.


## Library imports

	R = require 'ramda'

	{both, identity, juxt, mergeAll, objOf, pipe, propIs, props, sum} = R


## Helper functions

	calculate_stop_byte = pipe(props(['bytes', 'start_byte']), sum, objOf('stop_byte'))

	calculate_and_merge = pipe(juxt([identity, calculate_stop_byte]), mergeAll)

	has_properties = both(propIs(Number, 'bytes'), propIs(Number, 'start_byte'))


## Exports

	module.exports = R.when(has_properties, calculate_and_merge)
