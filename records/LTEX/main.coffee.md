# LTEX

- [LTEX @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/LTEX)


## Imports

### Library imports

	{applySpec} = require 'ramda'


### Relative imports

	buffer_to = require '../../buffer_to/main'


## Exports

	module.exports =
		EDID: buffer_to.ascii

		GNAM: buffer_to.uint32 0

		HNAM: applySpec
			friction: buffer_to.uint8 0
			restitution: buffer_to.uint8 1

		MNAM: buffer_to.uint32 0

		SNAM: buffer_to.uint8 0

		TNAM: buffer_to.uint32 0
