# Test GMST (game setting) fields

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	gmst = require '../../records/gmst'


## Run

	tests = [
		input:
			buffer: Buffer.concat [
				buffer_from.ascii 'EDID'
				buffer_from.uint16 22
				buffer_from.ascii 'sKarmicTitleNeutral30'
				buffer_from.uint8 0
				buffer_from.ascii 'DATA'
				buffer_from.uint16 12
				buffer_from.ascii 'True Mortal'
				buffer_from.uint8 0
			]
			last_byte: 0

		output: [
			length: 28
			name: 'EDID'
			start_byte: 0
			stop_byte: 28
			value: 'sKarmicTitleNeutral30'
		,
			length: 18
			name: 'DATA'
			start_byte: 28
			stop_byte: 46
			value: 'True Mortal'
		]
	]

	tape 'Read GMST record fields', (t)->
		t.plan tests.length

		for test_data in tests
			# actual_output = gmst test_data.input
			actual_output = false

			t.deepEqual actual_output, test_data.output
