# ASPC (acoustic space)

## Imports

	buffer_to = require './buffer_to'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		BNAM: buffer_to.uint32 0

		OBND_: (value, record)->
			x1: value.readInt16LE 0
			y1: value.readInt16LE 2
			z1: value.readInt16LE 4
			x2: value.readInt16LE 6
			y2: value.readInt16LE 8
			z2: value.readInt16LE 10

		RDAT: buffer_to.uint32 0

		SNAM: buffer_to.uint32 0


## Links

- [ASPC @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/ASPC)

- [OBND field @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/OBND_Field)
