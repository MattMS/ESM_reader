# Write Fallout 3 data to a file

## Library imports

	fs = require 'fs'

	R = require 'ramda'

	{always, cond, converge, identity, invoker, join, nth, pipe, prop, T, unapply} = R

	through2 = require 'through2'


## Relative imports

	get_storage_from_read_object = require './get_storage_from_read_object'

	transform_stream = require './transform_stream'


## Object to key-value text Stream

	object_reader = through2.obj (chunk, encoding, done)->
		if not R.is(Object)(chunk)
			console.error('Chunk is not an Object.')

		else
			pair = get_storage_from_read_object(chunk)

			line = converge(unapply(join('')), [
				nth(0),
				always('='),
				nth(1),
				always('\n')
			])(pair)

			this.push(line)

		done()


## Run!

	file_path = process.argv[2]

	#output_file_path = "#{file_path}.txt"
	output_file_path = 'out.txt'

	#out = fs.open output_file_path, 'w', (err, fd)->

	write_stream = fs.createWriteStream output_file_path

	object_reader.pipe write_stream

	transformer = transform_stream()

	transformer.pipe object_reader

	read_stream = fs.createReadStream file_path

	read_stream.pipe transformer
