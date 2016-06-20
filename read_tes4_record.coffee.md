# Read TES4 record

## Library imports

	R = require 'ramda'


## Relative imports

	read_fields = require './read_fields'

	read_record_header = require './read_record_header'

	tes4 = require './records/tes4'


## Exports

	module.exports = (state)->
		header = read_record_header state

		last_byte_in_record = header.stop_byte + header.bytes

		data = read_fields tes4,
			buffer: state.buffer.slice header.stop_byte, last_byte_in_record
			last_byte: header.stop_byte

		R.prepend header, data


### Input schema

```
properties:
	buffer:
		type: 'Buffer'
	last_byte:
		minimum: 0
		type: 'integer'
type: 'object'
```


### Output schema

```
items: [
	$ref: './read_record_header'
,
	$ref: './read_fields'
]
type: 'array'
```
