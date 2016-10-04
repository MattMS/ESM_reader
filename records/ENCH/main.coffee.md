# ENCH (enchantment)

- [ENCH @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/ENCH)


## Imports

	buffer_to = require '../../buffer_to/main'

	OBND = require '../../values/OBND/main'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		EFID: buffer_to.uint32 0

		#ENIT:

		FULL: buffer_to.uint32 0

		OBND: OBND
