# Get type name

## Exports

	module.exports = (buffer)->
		new Promise (resolve, reject)->
			if buffer.length < 4
				reject 'Missing data.'

			else
				part = buffer.slice 0, 4

				resolve part.toString 'ascii'
