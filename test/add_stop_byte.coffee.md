# Add stop byte

## Library imports

	tape = require 'tape'


## Relative imports

	add_stop_byte = require '../add_stop_byte'


## Run tests

	tape 'Add empty stop byte', (t)->
		t.plan 1
		actual_output = add_stop_byte {}
		t.deepEqual actual_output, {}

	tape 'Add valid stop byte', (t)->
		t.plan 1

		actual_output = add_stop_byte
			bytes: 24
			start_byte: 12

		desired_output =
			bytes: 24
			start_byte: 12
			stop_byte: 36

		t.deepEqual actual_output, desired_output
