# Read Fallout 3 data files

## Library imports

	fs = require 'fs'

	R = require 'ramda'

	{cond, converge, identity, invoker, join, pipe, prop, T, unapply} = R

	redis = require 'redis'

	stream = require 'stream'

	through2 = require 'through2'


## Relative imports

	get_storage_from_read_object = require './get_storage_from_read_object'

	transform_stream = require './transform_stream'


## Create Redis Stream and Client

	prefix =
		if 4 <= process.argv.length
			process.argv[3]
		else
			''

	redis_client = redis.createClient()

	redis_stream = through2.obj (chunk, encoding, done)->
		if not R.is(Object)(chunk)
			console.error('Chunk is not an Object.')

		else
			pair = get_storage_from_read_object(chunk)

			name = pair[0]

			if '' != prefix
				name = "#{prefix}.#{name}"

			value = pair[1]

			redis_client.set name, value, (err, res)->
				if err
					console.error err

				done()


## Run!

	file_path = process.argv[2]

	#output_file_path = "#{file_path}.txt"
	output_file_path = 'out.txt'

	write_stream = fs.createWriteStream output_file_path

	redis_stream.pipe write_stream

	buffer_to_object_stream = transform_stream()

	buffer_to_object_stream.pipe redis_stream

	read_stream = fs.createReadStream file_path

	read_stream.pipe buffer_to_object_stream
