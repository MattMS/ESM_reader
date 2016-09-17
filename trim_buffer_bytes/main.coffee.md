# Trim buffer bytes

## Library imports

	{always, assoc, converge, identity, invoker, prop} = require 'ramda'


## Drop from buffer

	drop_from_buffer = invoker(1, 'slice')


## Exports

	module.exports = converge(assoc, [
		always('buffer'),
		converge(drop_from_buffer, [
			prop('bytes'),
			prop('buffer')
		]),
		identity
	])
