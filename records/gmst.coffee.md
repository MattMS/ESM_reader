# GMST (game setting)

## Imports

	buffer_to = require '../buffer_to'

	#R = require 'ramda'


## Exports

	module.exports =
		after: (fields)->
			if 2 <= fields.length
				value = fields[1].value

				value_type = fields[0].value[0]

				#value_type = edid_ascii[0]
				#value_type = String.fromCharCode buffer[0]

				fields[1].value =
					if 'b' == value_type
						0 < value.readUInt32LE 0

					else if 'i' == value_type
						#buffer_to.uint32 0
						value.readUInt32LE 0

					else if 'f' == value_type
						#buffer_to.float 0
						value.readFloatLE 0

					else if 's' == value_type
						buffer_to.ascii value

					else
						value

			fields

		EDID: buffer_to.ascii


## Links

- [GMST 5 @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/GMST)
