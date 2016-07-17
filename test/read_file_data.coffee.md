# Test read file data

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_file_data = require '../read_file_data'


## Test data

	tests = [
		input:
			buffer: Buffer.concat [
				buffer_from.ascii 'TES4'
				buffer_from.uint32 18
				buffer_from.uint32 0
				buffer_from.uint32 0
				buffer_from.uint16 0
				buffer_from.uint16 0
				buffer_from.uint32 0

				buffer_from.ascii 'HEDR'
				buffer_from.uint16 12
				buffer_from.float 0.94
				buffer_from.uint32 7
				buffer_from.uint32 8

				buffer_from.ascii 'GRUP'
				buffer_from.uint32 0
				buffer_from.ascii 'GMST'
				buffer_from.uint32 3
				buffer_from.uint8 9
				buffer_from.uint8 72
				buffer_from.uint16 6
				buffer_from.uint16 7
				buffer_from.uint16 8
			]

			data: []
			group_label: 'TES4'
			group_number: 0
			group_stop_byte: 0
			last_byte: 0
			# record_name: 'TES4'
			record_number: 0
			record_stop_byte: 0

		output:
			buffer: new Buffer 0

			data: [
				bytes: 24
				data_bytes: 18
				group_number: 1
				id: 0
				name: 'TES4'
				record_number: 0
				revision: 0
				start_byte: 0
				stop_byte: 24
				unknown: 0
				version: 0
			,
				bytes: 18
				group_label: 'TES4'
				group_number: 1
				name: 'HEDR'
				record_name: 'TES4'
				record_number: 1
				start_byte: 24
				stop_byte: 42
				value:
					next_object_id: 8
					record_count: 7
					version: '0.94'
			,
				bytes: 24
				data_bytes: 0
				group_number: 2
				label: 'GMST'
				label_type: 3
				last_edit_date: '2009-01-09'
				last_edit_day_of_month: 9
				last_edit_month_since_2002_12: 72
				name: 'GRUP'
				record_number: 1
				start_byte: 42
				stop_byte: 66
				version: 7
			]

			group_label: 'GMST'
			group_number: 2
			group_stop_byte: 66
			last_byte: 66
			record_name: 'TES4'
			record_number: 1
			record_stop_byte: 42
	]


## Run

	tape 'Read file data', (t)->
		t.plan tests.length

		for test_data in tests
			desired_output = test_data.output

			actual_output = read_file_data test_data.input

			t.deepEqual actual_output, desired_output
