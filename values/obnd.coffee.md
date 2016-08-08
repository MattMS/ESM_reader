# OBND (object bounds)

## Library imports

	{applySpec} = require 'ramda'


## Relative imports

	buffer_to = require '../buffer_to'


## Exports

	module.exports = applySpec
		x1: buffer_to.int16 0
		y1: buffer_to.int16 2
		z1: buffer_to.int16 4
		x2: buffer_to.int16 6
		y2: buffer_to.int16 8
		z2: buffer_to.int16 10


## Links

- [OBND @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/OBND_Field)
