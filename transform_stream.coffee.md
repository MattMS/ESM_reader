# Read file stream

## Library imports

	bunyan = require 'bunyan'

	{isEmpty, last, pick} = require 'ramda'

	through2 = require 'through2'


## Relative imports

	read_file_data = require './read_file_data'


## Logging

	log = bunyan.createLogger
		name: 'transform_stream'
		serializers:
			state: pick([
				'buffer'
				'group_number'
				'group_stop_byte'
				'record_number'
				'record_stop_byte'
				'stop_byte'
			])


## Exports

	module.exports = ->
		state =
			buffer: new Buffer 0
			group_label: 'TES4'
			group_number: 0
			group_stop_byte: 0
			# record_name: 'TES4'
			record_number: 0
			record_stop_byte: 0
			stop_byte: 0

> Note that `through2.obj(fn)` is a convenience wrapper around `through2({objectMode: true}, fn)`.

		through2.obj (chunk, encoding, done)->
			if not Buffer.isBuffer(chunk)
				log.error('Chunk is not a Buffer.')

			else
				state.buffer = Buffer.concat([state.buffer, chunk])

				results = read_file_data(state)

				if not isEmpty(results)
					state = last(results)


### Output values

Loop over the results returned and push each out to the Stream consumer.

				transformer = this

				results.forEach (item)->
					transformer.push item


### End

			done()
