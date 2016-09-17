# Start log

Use this module to setup logging in other modules.

Returns an Object with console-style functions.

Attached is a `base` property that can be sent to `pipe_to_stream` and `stop` on a flogging.js base.


## Library imports

	flogging_base = require 'flogging.stream_base'

	flogging_console_inputs = require 'flogging.console_inputs'

	{pipe} = require 'ramda'


## Exports

	module.exports = (transform)->
		logger = flogging_base.start()

		sender = pipe transform, flogging_base.send logger

		log = flogging_console_inputs flogging_base.make_note, sender

		log.base = logger

		log
