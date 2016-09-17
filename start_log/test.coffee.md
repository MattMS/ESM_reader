# Test start log

# Imports

### Library imports

	concat_stream = require 'concat-stream'

	{identity} = require 'ramda'

	{pipe_to_stream, stop} = require('flogging.stream_base')

	tape = require 'tape'


### Relative imports

	start_log = require './main'


## Run tests

### Simple log

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

		log = start_log identity

		log_stream = concat_stream (actual_output)->
			t.deepEqual actual_output, desired_output, 'Log messages match'

		pipe_to_stream log.base, log_stream

		log.info 'First', 1
		log.warn 'Second', 2
		log.error 'Third', 4

		stop log.base


### With transform

	tape 'Start log with transform', (t)->
		t.plan 1

		desired_output = [
			label: 'First'
			name: 'my logger'
			type: 'info'
			value: 1
		,
			label: 'Second'
			name: 'my logger'
			type: 'warn'
			value: 2
		,
			label: 'Third'
			name: 'my logger'
			type: 'error'
			value: 4
		]

		log = start_log (message)->
			message.name = 'my logger'
			message

		log_stream = concat_stream (actual_output)->
			t.deepEqual actual_output, desired_output, 'Log messages match'

		pipe_to_stream log.base, log_stream

		log.info 'First', 1
		log.warn 'Second', 2
		log.error 'Third', 4

		stop log.base
