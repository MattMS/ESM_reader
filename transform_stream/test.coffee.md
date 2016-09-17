# Test transform stream

# Imports

### Library imports

	concat_stream = require 'concat-stream'

	flogging_base = require 'flogging.stream_base'

	tape = require 'tape'

	through2 = require 'through2'


### Relative imports

	buffer_from = require '../buffer_from'

	common_file = require '../test/common_file'

	transform_stream = require './main'


## Run test

	tape 'Transform Stream', (t)->
		t.plan 1

		desired_output = common_file.output


### Prepare Transform Stream with logging

		log = transform_stream.log

		log_stream = concat_stream (log_messages)->
			# console.log log_messages

		flogging_base.pipe_to_stream(log.base, log_stream)

		transformer = transform_stream()

		transformer.on 'end', ->
			flogging_base.stop(log.base)


### Collect and compare transformed output

		out_stream = concat_stream (actual_output)->
			t.deepEqual actual_output, desired_output

		transformer.pipe out_stream


### Pipe in test file data

		in_stream = through2()

		in_stream.pipe transformer

		in_stream.push common_file.input
		in_stream.push null
