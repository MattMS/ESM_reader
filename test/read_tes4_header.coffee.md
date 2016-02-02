# Test TES4 header reader

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_tes4_header = require '../read_tes4_header'


## Run

	tests = [
		input:
			buffer: Buffer.concat [
				buffer_from.ascii 'TES4'
				buffer_from.uint32 1
				buffer_from.uint32 2
				buffer_from.uint32 3
				buffer_from.uint16 4
				buffer_from.uint16 5
				buffer_from.uint32 6
			]
			last_byte: 0

		output:
			length: 24
			name: 'TES4'
			start_byte: 0
			stop_byte: 24
	]

	for test_data in tests
		tape 'Read TES4 header', (t)->
			t.plan 1

			desired_output = test_data.output

			actual_output = read_tes4_header test_data.input

			t.deepEqual actual_output, desired_output
