# ASPC (acoustic space)

## Imports

	buffer_to = require '../../buffer_to/main'

	obnd = require '../../values/obnd'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		BNAM: buffer_to.uint32 0

		OBND: obnd

		RDAT: buffer_to.uint32 0

		SNAM: buffer_to.uint32 0


## Links

- [ASPC @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/ASPC)
