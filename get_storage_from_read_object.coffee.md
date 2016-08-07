# Get storage from read object

Get the key-value pair for the object that can be used to store it.


## Library imports

	R = require 'ramda'

	{cond, converge, identity, invoker, join, juxt, pipe, prop, T, unapply} = R


## Relative imports

	limit_group_label = require './limit_group_label'


## Exports

	module.exports = juxt([
		converge(unapply(join('.')), [
			pipe(
				prop('group_label'),
				limit_group_label
			),
			prop('group_number'),
			prop('record_name'),
			prop('record_number'),
			prop('name')
		]),

		pipe(
			prop('value'),
			cond([
				[Buffer.isBuffer, invoker(1, 'toString')('hex')]
				[R.is(Object), JSON.stringify],
				[T, identity]
			])
		)
	])
