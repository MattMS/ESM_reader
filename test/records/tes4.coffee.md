# Test TES4 fields

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	tes4 = require '../../records/tes4'


## Run

	tests = [
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
			value:
				next_object_id: 8
				record_count: 7
				version: '0.94'
	]

	tape 'Read TES4 record fields', (t)->
		t.plan tests.length

		for test_data in tests
			# actual_output = tes4 test_data.input
			actual_output = false

			t.deepEqual actual_output, test_data.output
