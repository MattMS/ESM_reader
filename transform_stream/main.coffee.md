# Transform stream

## Library imports

	{identity, isEmpty, last, pick} = require 'ramda'

	through2 = require 'through2'


## Relative imports

	get_initial_state = require '../get_initial_state/main'

	parse_file_data = require '../parser/main'

	pipe_logs = require '../pipe_logs/main'

	start_log = require '../start_log/main'


## Logging

	log = start_log identity

	pipe_logs [
		parse_file_data.log
	], log


## Exports

	module.exports = ->
		state = get_initial_state()

> Note that `through2.obj(fn)` is a convenience wrapper around `through2({objectMode: true}, fn)`.

		through2.obj (chunk, encoding, done)->
			if not Buffer.isBuffer(chunk)
				log.error('Chunk is not a Buffer', chunk)

			else
				state.buffer = Buffer.concat([state.buffer, chunk])

				log.trace('Byte size of Buffer to parse', state.buffer.length)

				results = parse_file_data(state)

				log.trace('Result count after parsing Buffer', results.length)

				if isEmpty(results)
					log.trace('No results returned from parsing current state', state)

				else
					state = last(results)

					log.trace('Bytes for next Buffer', state.buffer.length)

				results.forEach (item)=>
					@push item

			done()

	module.exports.log = log
