# GRUP (group)

## File structure

- 4 ASCII bytes: `GRUP`

- 4 byte unsigned integer: size of group (including the 24 byte header).

- 4 ASCII bytes: label

- 4 byte unsigned integer

- 2 byte unsigned integer: last edit day of month

- 2 byte unsigned integer: last edit month since December 2002

- 6 bytes


## Constants

	GRUP_DETAILS_LENGTH = 16


## Imports

	check_type_name = require './check_type_name'

	grup_record = require './grup_record'


## Exports

	module.exports = (state)->
		start_grup state


	read_grup_details = (state)->
		if state.buffer.length < GRUP_DETAILS_LENGTH
			Promise.reject 'Missing data for GRUP details.'

		else
			new Promise (resolve, reject)->
				label = state.buffer.slice(0, 4).toString 'ascii'

				#grup_type = state.buffer.readUInt32LE 4

				day_of_month = state.buffer.readUInt8 8

				month_since_2002_12 = state.buffer.readUInt8 9

				last_edit = new Date 2002, 12, 1

				last_edit.setMonth month_since_2002_12

				last_edit.setDate day_of_month + 1

				last_edit_text = last_edit.toISOString().slice 0, 10


				state.emit "#{state.path}.label", label

				#.then state.emit "#{state.path}.type", grup_type

				.then state.emit "#{state.path}.last_edit.date", last_edit_text

				.then state.emit "#{state.path}.last_edit.day_of_month", day_of_month

				.then state.emit "#{state.path}.last_edit.month_since_2002_12", month_since_2002_12

				#.then state.emit "#{state.path}.unknown_1", state.buffer.slice 10, 14

				#.then state.emit "#{state.path}.version", state.buffer.readFloatLE(14, 18).toFixed 2

				#.then state.emit "#{state.path}.unknown_2", state.buffer.slice 18, 22

				.then -> 
					state.buffer = state.buffer.slice GRUP_DETAILS_LENGTH

					state.bytes_read += GRUP_DETAILS_LENGTH

					state.call = read_grup_record

					resolve state

				.catch (err)->
					state.call = null

					reject err


	read_grup_record = (state)->
		if state.bytes_read >= state.grup_end
			Promise.resolve stop_grup state

		else
			grup_record state


	start_grup = (state)->
		new Promise (resolve, reject)->
			check_type_name state.buffer, 'GRUP'

			.then (buffer)->
				if buffer.length < 4
					reject 'Missing data for GRUP.'

				else
					if state.grup_count
						state.grup_count += 1
					else
						state.grup_count = 1

					state.path = "GRUP.#{state.grup_count}"

Get the number of bytes in this entire block.

					grup_bytes_to_read = buffer.readUInt32LE 0

					state.emit "#{state.path}.bytes", grup_bytes_to_read

					.then ->
						state.buffer = buffer.slice 4

						state.grup_end = state.bytes_read + grup_bytes_to_read

						state.bytes_read += 8

						state.call = read_grup_details

						resolve state

			.catch (err)->
				state.call = null

				reject err


	stop_grup = (state)->
		#console.log state.bytes_read.toString(16), state.grup_end.toString(16)

		delete state.grup_end

		delete state.record_count

		state.call = start_grup

		state


## Links

- [Groups File Format @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Groups)
