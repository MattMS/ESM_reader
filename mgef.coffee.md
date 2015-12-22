# MGEF (magic effect)

## Imports

	buffer_to = require './buffer_to'


## Exports

	module.exports =
		DATA: (value, record)->
			#base_cost: buffer_to.float 4
			#flags: value.slice 0, 4
			#related_id: buffer_to.uint32 8
			#resistance_skill: buffer_to.uint32 16
			#skill: buffer_to.uint32 12

			value

		DESC: buffer_to.ascii

		EDID: buffer_to.ascii

		FULL: buffer_to.ascii

		MDOB: buffer_to.uint32 0


## Links

- [MGEF @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/MGEF)
