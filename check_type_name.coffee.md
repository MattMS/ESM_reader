# Check type name

## Imports

	get_type_name = require './get_type_name'


## Exports

	module.exports = (buffer, name_wanted)->
		new Promise (resolve, reject)->
			get_type_name buffer

			.then (name_found)->
				if name_found != name_wanted
					reject "Instead of #{name_wanted} got: #{name_found}."
				else
					resolve buffer.slice 4

			.catch (err)->
				reject err
