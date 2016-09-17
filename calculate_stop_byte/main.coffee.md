# Calculate stop byte

## Library imports

	{juxt, path, pipe, prop, sum} = require 'ramda'


## Exports

	module.exports = pipe(
		# props(['data_bytes', 'stop_byte']),
		juxt([
			path(['value', 'data_bytes']),
			prop('stop_byte')
		])
		sum
	)
