# Read file stream

## Library imports

	bunyan = require 'bunyan'

	R = require 'ramda'

	stream = require 'stream'

	through2 = require 'through2'


## Relative imports

	read_fields = require './read_fields'

	read_group_header = require './read_group_header'

	read_record_header = require './read_record_header'

	read_tes4_record = require './read_tes4_record'

	# tes4 = require './records/tes4'


## Logging

	log = bunyan.createLogger
		name: 'transform_stream'
		serializers:
			state: (state)->
				buffer: state.buffer
				group_stop_byte: state.group_stop_byte
				last_byte: state.last_byte
				record_stop_byte: state.record_stop_byte


## Helper functions

	# ensure_array = R.unless(R.isArrayLike, R.of)

	ensure_array = (result)->
		if Array.isArray result
			result
		else if result isnt {}
			[result]
		else
			[]


## Exports

	module.exports = ->
		state =
			buffer: new Buffer 0
			group_stop_byte: 0
			last_byte: 0
			reader: read_tes4_record
			record_stop_byte: 0

> Note that `through2.obj(fn)` is a convenience wrapper around `through2({objectMode: true}, fn)`.

		through2.obj (chunk, encoding, done)->
			if not Buffer.isBuffer chunk
				log.error 'Chunk is not a Buffer.'

			else
				state.buffer = Buffer.concat [state.buffer, chunk]

				while true
					result = ensure_array state.reader
						buffer: state.buffer
						last_byte: state.last_byte

					log.info state: state
					log.info result: result

An empty result means that more data is required.

					if R.isEmpty(result) or R.isEmpty(result[0])
						log.info 'Result was an empty array.'
						break

Save the last byte index from the results.

					last_byte = result[result.length - 1].stop_byte

					log.info "Last byte of result is #{last_byte}."

Trim the bytes that were read into results.

					# trim_bytes = result[result.length - 1].stop_byte - result[0].start_byte
					trim_bytes = last_byte - state.last_byte

					log.info "Trimming #{trim_bytes} bytes from Buffer."

					state.buffer = state.buffer.slice trim_bytes

					log.info "Buffer size is now #{state.buffer.length}."

					state.last_byte = last_byte


### Reading group

Store the last byte for the group if it has not been found yet.

					if not state.group_stop_byte
						log.info 'No group stop byte, start reading group header.'

						state.group_stop_byte = state.last_byte + result[0].bytes
						state.reader = read_group_header

Check if the results extend beyond the current group.

					else if state.last_byte >= state.group_stop_byte
						log.info 'Last byte is after group stop byte, start reading group header.'

						state.group_stop_byte = 0
						state.reader = read_group_header

					else if state.reader is read_group_header
						log.info 'Was reading group header, start reading record header.'

						state.reader = read_record_header


### Reading record

Store the last byte for the record if it has not been found yet.

					else if not state.record_stop_byte
						log.info 'No record stop byte, start reading record header.'

						state.record_stop_byte = state.last_byte + result[0].bytes
						state.reader = read_record_header

Check if the results extend beyond the current record.

					else if state.last_byte >= state.record_stop_byte
						log.info 'Last byte is after record stop byte, start reading record header.'

						state.record_stop_byte = 0
						state.reader = read_record_header

					else if state.reader is read_record_header
						log.info 'Was reading record header, start reading fields.'

						state.reader = read_fields


### Output values

Loop over the results returned and push each out to the Stream consumer.

					transformer = this

					result.forEach (item)->
						transformer.push item

					# this.push result


### End

			done()
