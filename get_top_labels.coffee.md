# Get labels of top-level groups in Redis

## Imports

	R = require 'ramda'

	redis = require 'redis'


## Run!

	redis_client = redis.createClient()

	show_error = (err)->
		console.error 'Redis error.'
		console.error err

		redis_client.quit()


	redis_client.on 'error', show_error


	redis_client.smembers 'GRUP', (err, result)->
		if err
			show_error err

		else
			keys = ("GRUP.#{k}.label" for k in result)

			redis_client.mget keys, (err, replies)->
				if err
					show_error err

				else
					diff = (a, b)-> a.localeCompare b

					for label in R.sort diff, replies
						console.log label

					redis_client.quit()
