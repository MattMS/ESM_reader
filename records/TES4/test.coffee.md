# Test TES4 fields

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	tes4 = require './main'


## Run tests

	tape 'Read TES4 HEDR field', (t)->
		t.plan 1

		actual_output = tes4.HEDR Buffer.concat [
			# buffer_from.ascii 'HEDR'
			# buffer_from.uint16 12
			buffer_from.float 0.94
			buffer_from.uint32 7
			buffer_from.uint32 8
		]

		desired_output =
			# bytes: 18
			# name: 'HEDR'
			# value:
			next_object_id: 8
			record_count: 7
			version: '0.94'

		t.deepEqual actual_output, desired_output
