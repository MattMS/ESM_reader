# Test read field

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_field = require '../read_field'


## Test data

	tests = [
		input: new Buffer 0
		output:
			bytes: 0

	,
		input: Buffer.concat [
			buffer_from.ascii 'HEDR'
			buffer_from.uint16 12
			buffer_from.float 0.94
			buffer_from.uint32 7
			buffer_from.uint32 8
		]

		output:
			bytes: 18
			name: 'HEDR'
			value: Buffer.concat [
				buffer_from.float 0.94
				buffer_from.uint32 7
				buffer_from.uint32 8
			]
	]


## Run

	tape 'Read field', (t)->
		t.plan tests.length

		for test_data in tests
			desired_output = test_data.output

			actual_output = read_field test_data.input

			t.deepEqual actual_output, desired_output
