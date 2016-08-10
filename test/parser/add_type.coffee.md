# Test add type

## Library imports

	tape = require 'tape'


## Relative imports

	add_type = require '../../parser/add_type'

	buffer_from = require '../../buffer_from'


## Run tests

	tape 'Add field type', (t)->
		t.plan 1

		actual_output = add_type('field')
			name: 'GMST'

		desired_output =
			name: 'GMST'
			type: 'field'

		t.deepEqual actual_output, desired_output


	tape 'Add group header type', (t)->
		t.plan 1

		actual_output = add_type('group_header')
			name: 'GRUP'

		desired_output =
			name: 'GRUP'
			type: 'group_header'

		t.deepEqual actual_output, desired_output


	tape 'Add record header type', (t)->
		t.plan 1

		actual_output = add_type('record_header')
			name: 'TES4'

		desired_output =
			name: 'TES4'
			type: 'record_header'

		t.deepEqual actual_output, desired_output
