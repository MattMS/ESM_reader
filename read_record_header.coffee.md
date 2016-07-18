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

	{always, applySpec, compose, ifElse, lte, propSatisfies, slice} = require 'ramda'


## Relative imports

	buffer_to = require './buffer_to'


## Check buffer size

	has_byte_length = propSatisfies(lte(24), 'length')


## Get values

Plain version of `label` is `buffer.slice(0, 4).toString('ascii')`.

	get_main_values = applySpec
		bytes: always 24
		data_bytes: buffer_to.uint32 4
		#flags = buffer.slice 8, 12
		id: buffer_to.uint32 12
		name: compose(buffer_to.ascii, slice(0, 4))
		revision: buffer_to.uint32 16
		unknown: buffer_to.uint16 22
		version: buffer_to.uint16 20


## Exports

	module.exports = ifElse(has_byte_length, get_main_values, always({bytes: 0}))


## Links

- [Records @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Records)
