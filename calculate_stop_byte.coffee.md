# Calculate stop byte

## Library imports

	R = require 'ramda'

	ramped = require 'ramped'


## Exports

	module.exports = ramped.evolve_with_input
		stop_byte: R.converge R.add, [
			R.prop 'start_byte'
			R.prop 'length'
		]
