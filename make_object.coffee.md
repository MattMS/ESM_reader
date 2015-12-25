# Make object

	module.exports = (items)->
		(value)->
			o = {}

			for n, v of items
				o[n] = v value

			o
