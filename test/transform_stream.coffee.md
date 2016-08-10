# Test Stream

## Library imports

	stream = require 'stream'

	tape = require 'tape'


## Relative imports

	buffer_from = require '../buffer_from'

	common_file = require './common_file'

	transform_stream = require '../transform_stream'


## Run test

	tape 'Transform Stream', (t)->
		t.plan 1

		desired_output = common_file.output

		test_data_input = common_file.input


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
