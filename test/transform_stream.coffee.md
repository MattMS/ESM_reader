# Test Stream

## Library imports

	stream = require 'stream'

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	transform_stream = require '../transform_stream'


## Run test

	tape 'Transform Stream', (t)->
		t.plan 1


### Input data

		test_data_input = Buffer.concat [
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


### Desired output

		desired_output = [
			buffer: Buffer.concat [
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
			value:
				data_bytes: 18
				id: 0
				revision: 0
				unknown: 0
				version: 0
		,
			buffer: Buffer.concat [
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
			bytes: 18
			field_counts:
				HEDR: 1
			field_number: 1
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
		,
			buffer: new Buffer(0)
			bytes: 24
			field_counts: {}
			group_label: 'GMST'
			group_number: 2
			group_stop_byte: 66
			name: 'GRUP'
			record_name: 'TES4'
			record_number: 1
			record_stop_byte: 42
			start_byte: 42
			stop_byte: 66
			value:
				data_bytes: 0
				label: 'GMST'
				label_type: 3
				last_edit_date: '2009-01-09'
				last_edit_day_of_month: 9
				last_edit_month_since_2002_12: 72
				version: 7
		]


### Collect and test transformed data

		actual_output = []

		transformer = transform_stream()

		transformer.on 'data', (chunk)->
			actual_output.push chunk

		transformer.on 'end', (a)->
			t.deepEqual actual_output, desired_output


### Pipe in test input

		in_stream = new stream.Readable()

		in_stream.pipe transformer

		in_stream.push test_data_input
		in_stream.push null
