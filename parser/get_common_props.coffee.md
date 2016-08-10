# Get common props

## Library imports

	{pick} = require 'ramda'


## Exports

	module.exports = pick([
		'buffer',
		'field_counts',
		'group_label',
		'group_name',
		'group_number',
		'group_stop_byte',
		'record_name',
		'record_number',
		'record_stop_byte',
		'stop_byte'
	])
