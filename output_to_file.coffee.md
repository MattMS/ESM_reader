# Write Fallout 3 data to a file

## Imports

	fs = require 'fs'

	process_stream = require './process_stream'

	R = require 'ramda'


## Run!

	file_path = process.argv[2]

	#output_file_path = "#{file_path}.txt"
	output_file_path = 'out.txt'

	#out = fs.open output_file_path, 'w', (err, fd)->

	write_stream = fs.createWriteStream output_file_path

	read_stream = fs.createReadStream file_path


	get_lines = (path, value)->
		if R.is String, value
			if R.is Array, name
				name = R.join '.', name

			if Buffer.isBuffer value
				value = value.toString 'hex'

			"#{name}=#{value}\n"

		#if R.is Object, value
		else
			#value = JSON.stringify value

			lines = ''

			for name, sub_value of value
				next_path = R.concat path, name

				lines += get_lines next_path, sub_value

			lines


	emit = (name, value)->
		new Promise (resolve, reject)->
			if Buffer.isBuffer value
				value = value.toString 'hex'

			else if R.is Object, value
				value = JSON.stringify value

			if R.is Array, name
				name = R.join '.', name

			line = "#{name}=#{value}\n"

			write_stream.write line, (err, written, string)->
				if err
					reject err
				else
					resolve()


	process_stream read_stream, emit
