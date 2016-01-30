# Test TES4 fields

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	read_fields = require '../../read_fields'

	tes4 = require '../../records/tes4'


## Run

	tests = [
		input:
			buffer: Buffer.concat [
				buffer_from.ascii 'HEDR'
				buffer_from.uint16 12
				buffer_from.float 0.94
				buffer_from.uint32 7
				buffer_from.uint32 8
			]
			last_byte: 12

		output: [
			length: 18
			name: 'HEDR'
			start_byte: 12
			stop_byte: 30
			value:
				next_object_id: 8
				record_count: 7
				version: '0.94'
		]
	]

	tape 'Read TES4 record fields', (t)->
		for test_data in tests
			t.plan 1

			actual_output = read_fields tes4, test_data.input

			t.deepEqual actual_output, test_data.output
