# Process Fallout 3 data Stream

## Imports

	grup = require './grup'

	grup_record = require './grup_record'


## Exports

	module.exports = (file_stream, emit)->
		last_state =
			buffer: new Buffer 0
			bytes_read: 0
			call: start_tes4_file

The file Stream will keep data until a listener is attached.

		file_stream.on 'data', (buffer)->
			read_data buffer

			.then (buffer)->
				#console.log "Got buffer of #{buffer.length} bytes. Pausing Stream."

				file_stream.pause()

				last_state.buffer = Buffer.concat [last_state.buffer, buffer]

				last_state.emit = emit


				process_data = ->
					if not last_state.call
						console.error 'No more data process functions.'

					else
						last_state.call last_state

						.then (state)->
							last_state = state

							#console.log 'Calling process_data again.'

							process_data()

						.catch (err)->
							console.error err

							#console.log 'Resuming Stream.'

							file_stream.resume()

				process_data()

			.catch (err)->
				console.error err

		file_stream.on 'end', ->
			console.error 'Stream ended!'


## Start file

	start_tes4_file = (state)->
		new Promise (resolve, reject)->
			state.call = read_tes4_record

			state.grup_end = 24 + state.buffer.readUInt32LE 4

			state.path = 'TES4'

			resolve state


## Read TES4 record fields

	read_tes4_record = (state)->
		if state.bytes_read >= state.grup_end
			delete state.grup_end

			state.call = grup

			Promise.resolve state

		else
			grup_record state


## Get Buffer as Promise

	read_data = (buffer)->
		if not buffer
			Promise.reject 'Buffer is empty.'
		else
			Promise.resolve buffer
