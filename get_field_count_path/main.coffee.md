# Get field count path

## Library imports

	R = require 'ramda'

	{pipe, prepend, prop} = R


## Exports

	module.exports = pipe(prop('name'), R.of, prepend('field_counts'))
