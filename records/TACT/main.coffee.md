# TACT (talking activator)

## Imports

	buffer_to = require '../../buffer_to/main'

	OBND = require '../../values/OBND/main'


## Exports

	module.exports =
		#DEST:

		EDID: buffer_to.ascii

		#FNAM:

		FULL: buffer_to.ascii

		KSIZ: buffer_to.uint32 0

		KWDA: buffer_to.uint32 0

		#MODL:

		OBND: OBND

		#PNAM:

		SNAM: buffer_to.uint32 0

		#VMAD:

		VNAM: buffer_to.uint32 0
