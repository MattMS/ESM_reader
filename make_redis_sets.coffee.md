# Make Redis sets

## Imports

	redis = require 'redis'


## Run!

	redis_client = redis.createClient()

	redis_client.on 'error', (err)->
		console.error 'Redis error.'
		console.error err

		redis_client.quit()

	key_count = 0

	scan = (cursor)->
		redis_client.scan cursor, (err, result)->
			if err
				console.error 'Redis error.'
				console.error err

				redis_client.quit()

			else
				cursor = result[0]
				keys = result[1]

				key_count += keys.length

				multi = redis_client.multi()

				for key in keys
					key_parts = key.split '.'

					for i in [1...key_parts.length]
						set_key = key_parts.slice(0, i).join('.')

						set_value = key_parts[i]

						multi = multi.sadd set_key, set_value

				multi.exec (err, replies)->
					if err
						console.error 'Redis error.'
						console.error err

						redis_client.quit()

					else
						if 0 < cursor
							scan cursor
						else
							console.log "Keys: #{key_count}"
							redis_client.quit()

	scan 0
