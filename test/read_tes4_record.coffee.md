# Test reading TES4 record

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	read_tes4_record = require '../read_tes4_record'


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
		]


### Extra bytes

	,
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

			buffer_from.ascii 'zzzzzz'
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
		]
	]


## Run tests

	tape 'Read TES4 record', (t)->
		t.plan tests.length

		for test_data in tests
			desired_output = test_data.output

			actual_output = read_tes4_record
				buffer: test_data.input
				last_byte: 0

			t.deepEqual actual_output, desired_output
