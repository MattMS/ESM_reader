# Increment field count based on name

## Library imports

	{always, converge, identity, ifElse, inc, isNil, lensPath, over, pipe} = require 'ramda'


## Relative imports

	get_field_count_path = require '../parser/get_field_count_path'


## Increment the count

	inc_from_1 = ifElse(isNil, always(1), inc)


## Get lens

	get_field_count_lens = pipe(get_field_count_path, lensPath)


## Exports

	module.exports = converge(over, [get_field_count_lens, always(inc_from_1), identity])
