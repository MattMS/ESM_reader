# TXST (texture set)

## Structure

- `EDID`

- 2: length of next field

- variable: ascii + `\0`

- `OBND`

- 14


### TX0? values

These are optional fields with labels `TX00` to `TX07`.

- `TX00` to `TX07`

- 2: length of next field

- variable: ascii + `\0`


### DNAM

- `DNAM`

- 4: unknown


## Imports

	buffer_to = require '../buffer_to'


## Exports

	module.exports =
		EDID: buffer_to.ascii
		TX00: buffer_to.file_path
		TX01: buffer_to.file_path
		TX02: buffer_to.file_path
		TX03: buffer_to.file_path
		TX04: buffer_to.file_path
		TX05: buffer_to.file_path
		TX06: buffer_to.file_path
		TX07: buffer_to.file_path

		#texture_labels = ("TX0#{i}" for i in [0..7])


## Links

- [TXST 5 @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format/TXST)
