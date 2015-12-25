# FACT (faction)

## Structure

- `EDID`

- 2: length of next field

- variable: ascii + `\0`


### FULL (optional)

- `FULL`

- 2: length of next field

- variable: ascii + `\0`


### XNAM (optional)

Can be multiple XNAM fields.

- `XNAM`

- 2: length of next fields; probably always `0C`

- 4: form ID

- 4: unused

- 4: combat flags


### DATA

- `DATA`

- 2: length of next field; probably always `04`

- 4

0x1 - Hidden from PC
0x2 - Special Combat
0x40 - Track Crime
0x80 - Ignore Murder
0x100 - Ignore Assault
0x200 - Ignore Stealing
0x400 - Ignore Trespass
0x800 - Do not report crimes against members
0x1000 - Crime Gold, Use Defaults
0x2000 - Ignore Pickpocket
0x4000 - Vendor
0x8000 - Can Be Owner
0x10000 - Ignore Werewolf


### RNAM

- `RNAM`

- 2: length of next field; probably always `04`

- 4


## Imports

	buffer_to = require '../buffer_to'


## Constants

	combat_names =
		0: 'neutral'
		1: 'enemy'
		2: 'ally'
		3: 'friend'


## Helper functions

	get_combat_name = (value)->
		combat_names[value] or value


## Exports

	module.exports =
		EDID: buffer_to.ascii

		FULL: buffer_to.ascii

		XNAM: (value, record)->
			if 12 <= value.length
				combat: get_combat_name value.readUInt32LE 8
				id: value.readUInt32LE 0
				unknown: value.readUInt32LE 4
			else
				value


## Links

- [FACT @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/FACT)
