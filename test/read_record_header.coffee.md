# Test read record header

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_record_header = require '../read_record_header'


## Run

	tests = [
		input: new Buffer 0
		output:
			bytes: 0

	,
		input: Buffer.concat [
			buffer_from.ascii 'GMST'
			buffer_from.uint32 0
			buffer_from.uint32 1
			buffer_from.uint32 2
			buffer_from.uint32 3
			buffer_from.uint16 4
			buffer_from.uint16 5
		]

		output:
			bytes: 24
			name: 'GMST'
			value:
				data_bytes: 0
				id: 2
				revision: 3
				unknown: 5
				version: 4
	]

	tape 'Read record header', (t)->
		t.plan tests.length

		for test_data in tests
			desired_output = test_data.output

			actual_output = read_record_header test_data.input

			t.deepEqual actual_output, desired_output
