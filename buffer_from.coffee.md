# Create a Buffer from other values

	module.exports =
		ascii: (value)->
			new Buffer value

		uint8: (value)->
			b = new Buffer 1
			b.writeUInt8 value, 0
			b

		uint16: (value)->
			b = new Buffer 2
			b.writeUInt16LE value, 0
			b

		uint32: (value)->
			b = new Buffer 4
			b.writeUInt32LE value, 0
			b
