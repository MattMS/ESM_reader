# Test Stream

## Library imports

	stream = require 'stream'

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	transform_stream = require '../transform_stream'


## Run

	tests = [
		input: Buffer.concat [
			buffer_from.ascii 'TES4'
			buffer_from.uint32 1
			buffer_from.uint32 2
			buffer_from.uint32 3
			buffer_from.uint16 4
			buffer_from.uint16 5
			buffer_from.uint32 6
			buffer_from.ascii 'HEDR'
			buffer_from.uint16 12
			buffer_from.float 0.94
			buffer_from.uint32 7
			buffer_from.uint32 8
		]

TODO: Decide on format for `group` and `record` tracking fields.

		output: [
			# group: 1
			length: 24
			name: 'TES4'
			# record: 1
			start_byte: 0
			stop_byte: 24
		,
			# group: 1
			length: 18
			name: 'HEDR'
			# record: 1
			start_byte: 24
			stop_byte: 42
			value:
				next_object_id: 8
				record_count: 7
				version: '0.94'
		]
	]

	for test_data in tests
		tape 'Read Stream', (t)->
			t.plan 1

			desired_output = test_data.output


### Collect transformed data

			actual_output = []

			transformer = transform_stream()

			transformer.on 'data', (chunk)->
				actual_output.push chunk

			transformer.on 'end', ->
				t.deepEqual actual_output, desired_output


### Pipe in test input

			in_stream = new stream.Readable()

			in_stream.pipe transformer

			in_stream.push test_data.input
			in_stream.push null
