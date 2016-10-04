# ACTI (activator)

## Imports

	buffer_to = require '../../buffer_to/main'

	obnd = require '../../values/OBND/main'


## Exports

	module.exports =
		#DEST:

		EDID: buffer_to.ascii

		#FNAM: buffer_to.uint16 0

		FULL: buffer_to.ascii

		KNAM: buffer_to.uint32 0

		KSIZ: buffer_to.uint32 0

		KWDA: buffer_to.uint32 0

		#MODL:

		OBND: obnd

		#PNAM:

		RNAM: buffer_to.ascii

		SNAM: buffer_to.uint32 0

		#VMAD:

		VNAM: buffer_to.uint32 0

		WNAM: buffer_to.uint32 0


## Links

- [ACTI @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/ACTI)
