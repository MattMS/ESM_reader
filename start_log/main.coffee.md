# Start log

Use this module to setup logging in other modules.

Returns an Object with console-style functions.

Attached is a `base` property that can be sent to `pipe_to_stream` and `stop` on a flogging.js base.


## Library imports

	flogging_base = require('flogging.stream_base')

	flogging_console_inputs = require('flogging.console_inputs')


## Exports

	module.exports = ->
		logger = flogging_base.start()

		log = flogging_console_inputs(flogging_base.make_note, flogging_base.send(logger))

		log.base = logger

		log
