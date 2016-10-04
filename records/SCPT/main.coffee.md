# SCPT (script)

## Imports

	buffer_to = require '../../buffer_to/main'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		#SCDA: compiled script data

		#SCHR: buffer_to.ascii

		SCRO: buffer_to.uint32 0

		SCTX: buffer_to.ascii

		SCVR: buffer_to.ascii


## Links

- [TES 4 SCPT @ UESP](http://www.uesp.net/wiki/Tes4Mod:Mod_File_Format/SCPT)
