# Set field number based on name

## Library imports

	{always, assoc, converge, identity, path} = require 'ramda'


## Relative imports

	get_field_count_path = require './get_field_count_path'


## Get value

	get_field_count_value = converge(path, [get_field_count_path, identity])


## Exports

	module.exports = converge(assoc, [always('field_number'), get_field_count_value, identity])
