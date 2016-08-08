# Test buffer-to

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_to = require '../buffer_to'


## Run tests

	tape 'buffer_to.ascii', (t)->
		t.plan 1
		actual = buffer_to.ascii(new Buffer [0x31, 0x41, 0x61, 0x0])
		t.deepEqual actual, '1Aa'

	tape 'buffer_to.int16 -1', (t)->
		t.plan 1
		actual = buffer_to.int16(0, new Buffer [0xFF, 0xFF])
		t.deepEqual actual, -1

	tape 'buffer_to.label', (t)->
		t.plan 1
		actual = buffer_to.label(0, new Buffer [0x47, 0x52, 0x55, 0x50])
		t.deepEqual actual, 'GRUP'

	tape 'buffer_to.uint8', (t)->
		t.plan 1
		actual = buffer_to.uint8(0, new Buffer [9])
		t.deepEqual actual, 9

	tape 'buffer_to.uint16', (t)->
		t.plan 1
		actual = buffer_to.uint16(0, new Buffer [1, 2])
		t.deepEqual actual, 513
