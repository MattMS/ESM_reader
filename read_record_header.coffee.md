# Read record header

## File structure

| Bytes | Type             | Description                         |
| ----- | ---------------- | ----------------------------------- |
|     4 | ASCII            | Label (usually matches group label) |
|     4 | unsigned integer | Size of record                      |
|     4 | -                | Bit flags                           |
|     4 | unsigned integer | Record ID                           |
|     4 | unsigned integer | Revision ID                         |
|     2 | unsigned integer | Version                             |
|     2 | -                | -                                   |


## Library imports

	R = require 'ramda'


## Relative imports

	buffer_to = require './buffer_to'


## Exports

Plain version of `label` is `buffer.slice(0, 4).toString('ascii')`.

	module.exports = R.applySpec
		bytes: R.always 24
		data_bytes: buffer_to.uint32 4
		#flags = buffer.slice 8, 12
		id: buffer_to.uint32 12
		name: R.compose(buffer_to.ascii, R.slice(0, 4))
		revision: buffer_to.uint32 16
		unknown: buffer_to.uint16 22
		version: buffer_to.uint16 20


## Links

- [Records @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Records)
