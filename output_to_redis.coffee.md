# Read Fallout 3 data files

## Imports

	fs = require 'fs'

	process_stream = require './process_stream'

	R = require 'ramda'

	redis = require 'redis'


## Run!

	file_path = process.argv[2]

	prefix =
		if 4 <= process.argv.length
			process.argv[3]
		else
			''


	read_stream = fs.createReadStream file_path

	redis_client = redis.createClient()


	read_stream.on 'end', ->
		redis_client.quit()


	emit = (name, value)->
		new Promise (resolve, reject)->
			if Buffer.isBuffer value
				value = value.toString 'hex'

			else if R.is Object, value
				value = JSON.stringify value

			if R.is Array, name
				name = R.join '.', name

			if '' != prefix
				name = "#{prefix}.#{name}"

			redis_client.set name, value, (err, res)->
				if err
					reject err
				else
					resolve()


	process_stream read_stream, emit
