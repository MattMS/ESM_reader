# SOUN (sound)

## Imports

	buffer_to = require './buffer_to'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		FNAM: buffer_to.file_path

		OBND_: (value, record)->
			x1: value.readInt16LE 0
			y1: value.readInt16LE 2
			z1: value.readInt16LE 4
			x2: value.readInt16LE 6
			y2: value.readInt16LE 8
			z2: value.readInt16LE 10


## Links

- [OBND field @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/OBND_Field)

- [SOUN @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/SOUN)
