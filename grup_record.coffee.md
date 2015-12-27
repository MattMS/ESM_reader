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

	buffer_to = require './buffer_to'

	get_field = require './get_field'

	handlers =
		ASPC: require './records/aspc'
		DOOR: require './records/door'
		FACT: require './records/fact'
		GLOB: require './records/glob'
		GMST: require './records/gmst'
		MGEF: require './records/mgef'
		SCPT: require './records/scpt'
		SOUN: require './records/soun'
		TES4: require './records/tes4'
		TXST: require './records/txst'

	make_object = require './make_object'

	R = require 'ramda'


## Exports

	module.exports = (state)->
		if not state.record
			if state.buffer.length < RECORD_DETAILS_LENGTH
				Promise.reject 'Missing data for record.'

			else
				new Promise (resolve, reject)->
					if state.record_count
						state.record_count += 1
					else
						state.record_count = 1

					state.record = read_record_header state.buffer

					state.record.end = state.bytes_read + RECORD_DETAILS_LENGTH + state.record.bytes

					state.record.field_count = {}

					state.record.fields = []

					state.record.path = [
						state.path
						state.record.label
						state.record_count
					]

					base_path = state.record.path.join '.'

					#make_path = R.compose R.join('.'), R.concat state.record.path
					#make_path('size')

					state.emit "#{base_path}.size", state.record.bytes

					.then -> state.emit "#{base_path}.id", state.record.id

					.then ->
						state.buffer = state.buffer.slice RECORD_DETAILS_LENGTH

						state.bytes_read += RECORD_DETAILS_LENGTH

						resolve state

					.catch (err)->
						console.error err

						state.call = null

						reject state

		else
			if state.buffer.length < state.record.bytes
				Promise.reject 'Missing data for record.'

			else if state.record.end <= state.bytes_read
				delete state.record

				Promise.resolve state

			else
				read_record_data state


	increment_field_count = (record, label)->
		if record.field_count[label]
			record.field_count[label] += 1
		else
			record.field_count[label] = 1


	get_field_path = (record, label)->
		R.concat record.path, [
			label
			record.field_count[label]
		]


	read_record_header = make_object
		#flags = buffer.slice 8, 12

		bytes: buffer_to.uint32 4
		id: buffer_to.uint32 12
		label: R.compose buffer_to.ascii, R.slice(0, 4)
		#label: buffer.slice(0, 4).toString 'ascii'
		#revision: buffer_to.uint32 16
		#unknown: buffer_to.uint16 22
		#version: buffer_to.uint16 20


	read_record_data = (state)->
		new Promise (resolve, reject)->
			{label, length, value} = get_field state.buffer

			increment_field_count state.record, label

			path = get_field_path state.record, label

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


## Links

- [Records @ UESP](http://www.uesp.net/wiki/Tes5Mod:Mod_File_Format#Records)
