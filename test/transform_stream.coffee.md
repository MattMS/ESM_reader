# Test Stream

## Library imports

	stream = require 'stream'

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	transform_stream = require '../transform_stream'


## Test data

	tests = [
		input: Buffer.concat [
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

		output: [
			bytes: 18
			id: 0
			label: 'TES4'
			length: 24
			revision: 0
			start_byte: 0
			stop_byte: 24
			unknown: 0
			version: 0
		,
			length: 18
			name: 'HEDR'
			start_byte: 24
			stop_byte: 42
			value:
				next_object_id: 8
				record_count: 7
				version: '0.94'
		,
			bytes: 0
			label: 'GMST'
			label_type: 3
			last_edit_date: '2009-01-09'
			last_edit_day_of_month: 9
			last_edit_month_since_2002_12: 72
			length: 24
			start_byte: 42
			stop_byte: 66
			version: 7
		]
	]


## Run tests

	tape 'Read Stream', (t)->
		t.plan tests.length

		actual_output = {}
		desired_output = {}

		for test_data in tests
			transformer = transform_stream()

			desired_output[transformer] = test_data.output


### Collect transformed data

			actual_output[transformer] = []

			transformer.on 'data', (chunk)->
				actual_output[this].push chunk

			transformer.on 'end', (a)->
				t.deepEqual actual_output[this], desired_output[this]


### Pipe in test input

			in_stream = new stream.Readable()

			in_stream.pipe transformer

			in_stream.push test_data.input
			in_stream.push null
