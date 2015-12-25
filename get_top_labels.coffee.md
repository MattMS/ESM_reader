# Get labels of top-level groups in Redis

## Imports

	R = require 'ramda'

	redis = require 'redis'


## Run!

	base_name =
		if 3 <= process.argv.length
			"#{process.argv[2]}.GRUP"
		else
			'GRUP'


	redis_client = redis.createClient()

	show_error = (err)->
		console.error 'Redis error.'
		console.error err

		redis_client.quit()


	redis_client.on 'error', show_error


	redis_client.smembers base_name, (err, result)->
		if err
			show_error err

		else if 0 == result.length
			console.error "No keys found in set: #{base_name}"
			redis_client.quit()

		else
			keys = ("#{base_name}.#{k}.label" for k in result)

			redis_client.mget keys, (err, replies)->
				if err
					show_error err

				else
					diff = (a, b)-> a.localeCompare b

					for label in R.sort diff, replies
						console.log label

					redis_client.quit()
