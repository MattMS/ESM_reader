# Test calculate stop byte

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	calculate_stop_byte = require '../../parser/calculate_stop_byte'


## Run tests

	tape 'Calculate stop byte', (t)->
		t.plan 1

		actual_output = calculate_stop_byte
			value:
				data_bytes: 24
			stop_byte: 12

		desired_output = 36

		t.deepEqual actual_output, desired_output
