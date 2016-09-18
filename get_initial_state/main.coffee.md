# Get initial state

This is the state Object that is passed in to the [parser](./main.coffee.md) when starting a new file.

	module.exports = ->
		buffer: new Buffer 0
		group_label: 'TES4'
		group_name: 'TES4'
		group_number: 0
		group_stop_byte: 0
		# record_name: 'TES4'
		record_number: 0
		record_stop_byte: 0
		stop_byte: 0
