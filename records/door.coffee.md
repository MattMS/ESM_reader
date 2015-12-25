# DOOR

## Imports

	buffer_to = require '../buffer_to'

	obnd = require '../values/obnd'


## Exports

	module.exports =
		ANAM: buffer_to.uint32 0

		BNAM: buffer_to.uint32 0

		EDID: buffer_to.ascii

		#FNAM: buffer_to.uint8 0

		FULL: buffer_to.ascii

		MODL: buffer_to.file_path

		#MODS:

		#MODT:

		OBND: obnd

		SNAM: buffer_to.uint32 0

		#VMAD:


## Links

- [DOOR @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/DOOR)

- [Model Textures Field @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/Model_Textures_Field)

- [MODL Field @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/MODL_Field)
