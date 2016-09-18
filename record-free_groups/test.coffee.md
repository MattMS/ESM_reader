# Test groups record-free groups

Test the groups that do not have any records, only fields.


## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	get_initial_state = require '../get_initial_state/main'

	parse_file_data = require '../parser/main'


## Run tests

	tape 'Record-free SCOL group', (t)->
		t.plan 1

		initial_state = get_initial_state()

		initial_state.buffer = Buffer.concat [
			buffer_from.ascii 'SCOL'
			buffer_from.uint32 12
			buffer_from.uint32 0
			buffer_from.uint32 0x318d0001
			buffer_from.uint8 9
			buffer_from.uint8 72
			buffer_from.uint16 6
			buffer_from.uint16 7
			buffer_from.uint16 8

			buffer_from.ascii 'EDID'
			buffer_from.uint16 6
			buffer_from.ascii 'abcdef'
		]

		initial_state.group_number = 2

		actual_output = parse_file_data initial_state

		desired_output = [
			buffer: Buffer.concat [
				buffer_from.ascii 'EDID'
				buffer_from.uint16 6
				buffer_from.ascii 'abcdef'
			]
			bytes: 24
			field_counts: {}
			# group_label: [0, 0, 0, 0]
			group_label: 0
			group_name: 'SCOL'
			group_number: 3
			group_stop_byte: 36
			name: 'SCOL'
			# record_name: 'TES4'
			record_number: 1
			record_stop_byte: 0
			start_byte: 0
			stop_byte: 24
			type: 'group_header'
			value:
				data_bytes: 12
				# label: [0, 0, 0, 0]
				label: 0
				label_type: 0x318d0001
				last_edit_date: '2009-01-09'
				last_edit_day_of_month: 9
				last_edit_month_since_2002_12: 72
				version: 7
		,
			buffer: Buffer.concat [
				buffer_from.ascii 'EDID'
				buffer_from.uint16 6
				buffer_from.ascii 'abcdef'
			]
			bytes: 24
			field_counts: {}
			group_label: 0
			group_name: 'SCOL'
			group_number: 3
			group_stop_byte: 36
			name: 'SCOL'
			record_name: 'SCOL'
			record_number: 1
			record_stop_byte: 36
			start_byte: 24
			stop_byte: 24
			type: 'record_header'
			value:
				data_bytes: 12
				id: 0
				revision: 0
				unknown: 0
				version: 0
		,
			buffer: new Buffer(0)
			bytes: 12
			field_counts:
				EDID: 1
			field_number: 1
			group_label: 0
			group_name: 'SCOL'
			group_number: 3
			group_stop_byte: 36
			name: 'EDID'
			record_name: 'SCOL'
			record_number: 1
			record_stop_byte: 36
			start_byte: 24
			stop_byte: 36
			type: 'field'
			# value: 'abcdef'
			value: buffer_from.ascii 'abcdef'
		]

		t.deepEqual actual_output, desired_output
