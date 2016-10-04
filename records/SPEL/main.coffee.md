# SPEL

- [SPEL @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/SPEL)


## Imports

### Library imports

	{applySpec} = require 'ramda'


### Relative imports

	buffer_to = require '../../buffer_to/main'

	OBND = require '../../values/OBND/main'


## Exports

	module.exports =
		DESC: buffer_to.ascii

		EDID: buffer_to.ascii

		EFID: buffer_to.uint32 0

		ETYP: buffer_to.uint32 0

		FULL: buffer_to.ascii

		MDOB: buffer_to.uint32 0

		OBND: OBND

		SPIT: applySpec
			cast_type: buffer_to.uint32 16
			cast_duration: buffer_to.float 24
			charge_time: buffer_to.float 12
			delivery: buffer_to.uint32 20
			flags: buffer_to.uint32 4
			half_cost_perk: buffer_to.uint32 32
			range: buffer_to.float 28
			spell_cost: buffer_to.uint32 0
			type: buffer_to.uint32 8
