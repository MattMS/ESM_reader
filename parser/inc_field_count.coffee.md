# Increment field count based on name

## Library imports

	R = require 'ramda'

	{always, converge, identity, ifElse, inc, isNil, lensPath, over, pipe, prepend, prop, tap} = R


## Increment the count

	inc_from_1 = ifElse(isNil, always(1), inc)


## Make the lens

	make_field_count_lens = pipe(prop('name'), R.of, prepend('field_counts'), lensPath)


## Exports

	module.exports = converge(over, [make_field_count_lens, always(inc_from_1), identity])
