# Test field count incrementor

## Library imports

	tape = require 'tape'


## Relative imports

	inc_field_count = require './main'


## Run tests

	tape 'Increment field count from empty', (t)->
		t.plan 1

		actual_output = inc_field_count
			field_counts: {}
			name: 'HEDR'

		desired_output =
			field_counts:
				HEDR: 1
			name: 'HEDR'

		t.deepEqual actual_output, desired_output


	tape 'Increment field count from 1', (t)->
		t.plan 1

		actual_output = inc_field_count
			field_counts:
				HEDR: 1
			name: 'HEDR'

		desired_output =
			field_counts:
				HEDR: 2
			name: 'HEDR'

		t.deepEqual actual_output, desired_output


	tape 'Increment field count of other', (t)->
		t.plan 1

		actual_output = inc_field_count
			field_counts:
				HEDR: 1
			name: 'CNAM'

		desired_output =
			field_counts:
				CNAM: 1
				HEDR: 1
			name: 'CNAM'

		t.deepEqual actual_output, desired_output
