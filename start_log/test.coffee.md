# Test start log

# Imports

### Library imports

	concat_stream = require 'concat-stream'

	{pipe_to_stream, stop} = require('flogging.stream_base')

	tape = require 'tape'


### Relative imports

	start_log = require './main'


## Run test

	tape 'Start log', (t)->
		t.plan 1

		desired_output = [
			label: 'First'
			type: 'info'
			value: 1
		,
			label: 'Second'
			type: 'warn'
			value: 2
		,
			label: 'Third'
			type: 'error'
			value: 4
		]

		log = start_log()

		log_stream = concat_stream (actual_output)->
			t.deepEqual actual_output, desired_output

		pipe_to_stream(log.base, log_stream)

		log.info('First', 1)
		log.warn('Second', 2)
		log.error('Third', 4)

		stop(log.base)
