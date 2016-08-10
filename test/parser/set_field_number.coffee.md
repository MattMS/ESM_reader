# Test set field number

## Library imports

	tape = require 'tape'


## Relative imports

	set_field_number = require '../../parser/set_field_number'


## Run tests

	tape 'Set field number with 1 count', (t)->
		t.plan 1

		actual_output = set_field_number
			field_counts:
				HEDR: 1
			name: 'HEDR'

		desired_output =
			field_counts:
				HEDR: 1
			field_number: 1
			name: 'HEDR'

		t.deepEqual actual_output, desired_output


	tape 'Set field number with 2 counts', (t)->
		t.plan 1

		actual_output = set_field_number
			field_counts:
				CNAM: 1
				HEDR: 2
			name: 'HEDR'

		desired_output =
			field_counts:
				CNAM: 1
				HEDR: 2
			field_number: 2
			name: 'HEDR'

		t.deepEqual actual_output, desired_output
