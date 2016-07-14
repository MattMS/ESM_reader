# Add stop byte

## Library imports

	tape = require 'tape'


## Relative imports

	add_stop_byte = require '../add_stop_byte'


## Test data

	tests = [
		input: {}
		output: {}
	,
		input:
			bytes: 24
			start_byte: 12
		output:
			bytes: 24
			start_byte: 12
			stop_byte: 36
	]


## Run tests

	tape 'Add stop byte', (t)->
		t.plan tests.length

		for test_data in tests
			desired_output = test_data.output

			actual_output = add_stop_byte test_data.input

			t.deepEqual actual_output, desired_output
