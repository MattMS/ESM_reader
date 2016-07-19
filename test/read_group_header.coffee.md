# Test group header reader

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_group_header = require '../read_group_header'


## Test data

	tests = [
		input: new Buffer 0
		output:
			bytes: 0

	,
		input: Buffer.concat [
			buffer_from.ascii 'GRUP'
			buffer_from.uint32 256
			buffer_from.ascii 'GMST'
			buffer_from.uint32 3
			buffer_from.uint8 9
			buffer_from.uint8 72
			buffer_from.uint16 6
			buffer_from.uint16 7
			buffer_from.uint16 8
		]

		output:
			bytes: 24
			name: 'GRUP'
			value:
				data_bytes: 256
				label: 'GMST'
				label_type: 3
				last_edit_date: '2009-01-09'
				last_edit_day_of_month: 9
				last_edit_month_since_2002_12: 72
				version: 7
	]


## Run

	tape 'Read group header', (t)->
		t.plan tests.length

		for test_data in tests
			desired_output = test_data.output

			actual_output = read_group_header test_data.input

			t.deepEqual actual_output, desired_output
