# Test parser

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	common_file = require '../test/common_file'

	get_initial_state = require '../get_initial_state/main'

	parse_file_data = require './main'


## Run tests

	tape 'Parse empty object', (t)->
		t.plan 1

		actual_output = parse_file_data {}

		desired_output = []

		t.deepEqual actual_output, desired_output


	tape 'Parse empty Buffer', (t)->
		t.plan 1

		actual_output = parse_file_data
			buffer: new Buffer(0)
			group_label: 'TES4'
			group_number: 0
			group_stop_byte: 0
			# record_name: 'TES4'
			record_number: 0
			record_stop_byte: 0
			stop_byte: 0

		desired_output = []

		t.deepEqual actual_output, desired_output


	tape 'Parse TES4 record with no fields', (t)->
		t.plan 1

		actual_output = parse_file_data
			buffer: Buffer.concat [
				buffer_from.ascii 'TES4'
				buffer_from.uint32 0
				buffer_from.uint32 0
				buffer_from.uint32 0
				buffer_from.uint16 0
				buffer_from.uint16 0
				buffer_from.uint32 0
			]
			group_label: 'TES4'
			group_number: 0
			group_stop_byte: 0
			# record_name: 'TES4'
			record_number: 0
			record_stop_byte: 0
			stop_byte: 0

		desired_output = [
			buffer: new Buffer(0)
			bytes: 24
			field_counts: {}
			group_label: 'TES4'
			group_number: 1
			group_stop_byte: 24
			name: 'TES4'
			record_name: 'TES4'
			record_number: 1
			record_stop_byte: 24
			start_byte: 0
			stop_byte: 24
			type: 'record_header'
			value:
				data_bytes: 0
				id: 0
				revision: 0
				unknown: 0
				version: 0
		]

		t.deepEqual actual_output, desired_output


	tape 'Parse full file', (t)->
		t.plan 1

		initial_state = get_initial_state()

		initial_state.buffer = common_file.input

		actual_output = parse_file_data initial_state

		desired_output = common_file.output

		t.deepEqual actual_output, desired_output
