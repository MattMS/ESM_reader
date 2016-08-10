# Get name path

## Library imports

	{cond, juxt, pipe, prop, propEq} = require 'ramda'


## Relative imports

	# limit_group_label = require '../limit_group_label'


## Limit group label property

	# limited_group_label = pipe(
	# 	prop('group_label'),
	# 	limit_group_label
	# )


## Exports

	module.exports = cond([
		[
			propEq('type', 'field'),
			juxt([
				# limited_group_label,
				prop('group_number'),
				prop('record_name'),
				prop('record_number'),
				prop('name'),
				prop('field_number')
			])
		]
		[
			propEq('type', 'group_header')
			juxt([
				# limited_group_label,
				prop('group_number')
			])
		]
		[
			propEq('type', 'record_header')
			juxt([
				# limited_group_label,
				prop('group_number'),
				prop('record_name'),
				prop('record_number')
			])
		]
	])
