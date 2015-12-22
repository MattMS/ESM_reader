# DOOR

## Imports

	buffer_to = require './buffer_to'


## Exports

	module.exports =
		ANAM: buffer_to.uint32 0
		BNAM: buffer_to.uint32 0
		EDID: buffer_to.ascii
		FULL: buffer_to.ascii
		MODL: buffer_to.file_path
		SNAM: buffer_to.uint32 0
