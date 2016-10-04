# Test GLOB

## Imports

### Library imports

	tape = require 'tape'


### Relative imports

	buffer_from = require '../../buffer_from'

	glob = require './main'


## Run tests

	tape 'GLOB EDID field', (t)->
		t.plan 1

		actual_output = glob.EDID buffer_from.ascii 'Some Text'

		desired_output = 'Some Text'

		t.deepEqual actual_output, desired_output


	tape 'GLOB FLTV field', (t)->
		t.plan 1

		actual_output = glob.FLTV buffer_from.float 12.5

		desired_output = 12.5

		t.deepEqual actual_output, desired_output
