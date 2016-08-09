# Test read file data

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	parse_field = require '../../parser/field'


## Run tests

	tape 'Parse TES4 HEDR field', (t)->
		t.plan 1

		actual_output = parse_field
			buffer: Buffer.concat [
				buffer_from.ascii 'HEDR'
				buffer_from.uint16 12
				buffer_from.float 0.94
				buffer_from.uint32 7
				buffer_from.uint32 8
			]
			bytes: 24
			field_counts: {}
			group_label: 'TES4'
			group_number: 1
			group_stop_byte: 42
			name: 'TES4'
			record_name: 'TES4'
			record_number: 1
			record_stop_byte: 42
			start_byte: 0
			stop_byte: 24

		desired_output =
			buffer: new Buffer(0)
			bytes: 18
			field_counts:
				HEDR: 1
			group_label: 'TES4'
			group_number: 1
			group_stop_byte: 42
			name: 'HEDR'
			record_name: 'TES4'
			record_number: 1
			record_stop_byte: 42
			start_byte: 24
			stop_byte: 42
			value:
				next_object_id: 8
				record_count: 7
				version: '0.94'

		t.deepEqual actual_output, desired_output
