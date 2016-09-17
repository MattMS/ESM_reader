# Pipe logs

Pipe multiple logs into a single log.


## Library imports

	{curry} = require 'ramda'


## Exports

	module.exports = curry (child_logs, parent_log)->
		for log in child_logs
			log.base.pipe parent_log.base

		parent_log
