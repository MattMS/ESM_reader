# Test pipe logs

# Imports

### Library imports

	concat_stream = require 'concat-stream'

	{pipe_to_stream, stop} = require('flogging.stream_base')

	tape = require 'tape'


### Relative imports

	pipe_logs = require './main'

	start_log = require '../start_log/main'


## Run test

	tape 'Pipe 2 logs into another', (t)->
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

		log_child_1 = start_log()

		log_child_2 = start_log()

		log_parent = start_log()

		pipe_logs [
			log_child_1
			log_child_2
		], log_parent

		log_stream = concat_stream (actual_output)->
			t.deepEqual actual_output, desired_output, 'Log messages match'

		pipe_to_stream log_parent.base, log_stream

		log_child_1.info 'First', 1
		log_child_2.warn 'Second', 2
		log_parent.error 'Third', 4

		stop log_child_1.base
		stop log_child_2.base
		stop log_parent.base
