# Test read field

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_field = require '../read_field'


## Run tests

	tape 'Read field from empty Buffer', (t)->
		t.plan 1

		actual_output = read_field new Buffer 0

		desired_output = bytes: 0

		t.deepEqual actual_output, desired_output

	tape 'Read HEDR field', (t)->
		t.plan 1

		actual_output = read_field Buffer.concat [
			buffer_from.ascii 'HEDR'
			buffer_from.uint16 12
			buffer_from.float 0.94
			buffer_from.uint32 7
			buffer_from.uint32 8
		]

		desired_output =
			bytes: 18
			name: 'HEDR'
			value: Buffer.concat [
				buffer_from.float 0.94
				buffer_from.uint32 7
				buffer_from.uint32 8
			]

		t.deepEqual actual_output, desired_output
