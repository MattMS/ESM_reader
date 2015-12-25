# GRUP record

## File structure

- 4 ASCII bytes: label (usually matches group label)

- 4 byte unsigned integer: size of record

- 4 byte bit flags

- 4 byte unsigned integer: record ID

- 4 byte unsigned integer: revision ID

- 2 byte unsigned integer: version

- 2 bytes


## Constants

	RECORD_DETAILS_LENGTH = 24


## Imports

	get_field = require './get_field'

	handlers =
		ASPC: require './aspc'
		DOOR: require './door'
		FACT: require './fact'
		GLOB: require './glob'
		GMST: require './gmst'
		MGEF: require './mgef'
		SCPT: require './scpt'
		SOUN: require './soun'
		TES4: require './tes4'
		TXST: require './txst'

	R = require 'ramda'


## Exports

	module.exports = (state)->
		if not state.record
			if state.buffer.length < RECORD_DETAILS_LENGTH
				Promise.reject 'Missing data for record.'

			else
				read_record state

		else
			if state.buffer.length < state.record.bytes
				Promise.reject 'Missing data for record.'

			else
				read_record_data state


	read_record = (state)->
		new Promise (resolve, reject)->
			label = state.buffer.slice(0, 4).toString 'ascii'

			if state.record_count
				state.record_count += 1
			else
				state.record_count = 1

			base_path = [
				state.path
				label
				state.record_count
			].join '.'

			item_bytes_to_read = state.buffer.readUInt32LE 4

			state.record =
				bytes: item_bytes_to_read
				end: state.bytes_read + RECORD_DETAILS_LENGTH + item_bytes_to_read
				field_count: {}
				fields: []
				id: state.buffer.readUInt32LE 12
				label: label
				path: [
					state.path
					label
					state.record_count
				]

			#make_path = R.compose R.join('.'), R.concat state.record.path
			#make_path('size')

			#flags = state.buffer.slice 8, 12

			state.emit "#{base_path}.size", state.record.bytes

			.then -> state.emit "#{base_path}.id", state.record.id

			#.then -> state.emit "#{base_path}.revision", state.buffer.readUInt32LE 16

			#.then -> state.emit "#{base_path}.version", state.buffer.readUInt16LE 20

			#.then -> state.emit "#{base_path}.unknown", state.buffer.readUInt16LE 22

			.then ->
				state.buffer = state.buffer.slice RECORD_DETAILS_LENGTH

				state.bytes_read += RECORD_DETAILS_LENGTH

				resolve state

			.catch (err)->
				console.error err

				state.call = null

				reject state


	read_record_data = (state)->
		new Promise (resolve, reject)->
			if state.bytes_read < state.record.end
				{label, length, value} = get_field state.buffer

				if state.record.field_count[label]
					state.record.field_count[label] += 1
				else
					state.record.field_count[label] = 1

				path = R.concat state.record.path, [
					label
					state.record.field_count[label]
				]

				if fixer = handlers[state.record.label]?[label]
					value = fixer value, state.record

				state.record.fields.push [path, value]

				state.emit path, value

				.then ->
					state.bytes_read += length

					state.buffer = state.buffer.slice length

					resolve state

				.catch (err)->
					console.error err

					state.call = null

					reject state

			else
				delete state.record

				resolve state


## Links

- [Records @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Records)
