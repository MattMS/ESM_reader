# Read file stream

## Library imports

	R = require 'ramda'

	stream = require 'stream'

	through2 = require 'through2'


## Relative imports

	read_fields = require './read_fields'

	read_record_header = require './read_record_header'

	tes4 = require './records/tes4'


## Helper functions

	call_after_byte = R.curry (next, byte, values)->

	call_on_object = R.curry (next, values)->


## Exports

	module.exports = ->
		state = last_byte: 0

> Note that `through2.obj(fn)` is a convenience wrapper around `through2({ objectMode: true }, fn)`.

		through2.obj (chunk, encoding, done)->
			if not Buffer.isBuffer chunk
				console.error 'Chunk is not a Buffer.'

				done()

			else
				result = read_tes4_header
					buffer: chunk
					last_byte: state.last_byte

				this.push result

				done()
