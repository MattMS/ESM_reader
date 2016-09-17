# Test get name path

## Library imports

	tape = require 'tape'


## Relative imports

	get_name_path = require './main'


## Run tests

	tape 'Get name path for field', (t)->
		t.plan 1

		actual_output = get_name_path
			field_number: 4
			name: 'HEDR'
			# group_label: 'TES4'
			group_number: 2
			record_name: 'TES4'
			record_number: 3
			type: 'field'

		desired_output = [2, 'TES4', 3, 'HEDR', 4]

		t.deepEqual actual_output, desired_output


	tape 'Get name path for group header', (t)->
		t.plan 1

		actual_output = get_name_path
			# group_label: 'TES4'
			group_number: 1
			type: 'group_header'

		desired_output = [1]

		t.deepEqual actual_output, desired_output


	tape 'Get name path for record header', (t)->
		t.plan 1

		actual_output = get_name_path
			# group_label: 'GMST'
			group_number: 2
			record_name: 'GMST'
			record_number: 3
			type: 'record_header'

		desired_output = [2, 'GMST', 3]

		t.deepEqual actual_output, desired_output
