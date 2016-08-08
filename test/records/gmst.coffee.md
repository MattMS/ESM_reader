# Test GMST (game setting) fields

## Library imports

	tape = require 'tape'


## Relative imports

	buffer_from = require '../../buffer_from'

	gmst = require '../../records/gmst'


## Run tests

	tape 'GMST EDID value', (t)->
		t.plan 1

		actual_output = gmst.EDID buffer_from.ascii 'sKarmicTitleNeutral30'

		desired_output = 'sKarmicTitleNeutral30'

		t.deepEqual actual_output, desired_output


	# tape 'GMST DATA value', (t)->
	# 	t.plan 1
	#
	# 	actual_output = gmst.DATA buffer_from.ascii 'True Mortal'
	#
	# 	desired_output = 'True Mortal'
	#
	# 	t.deepEqual actual_output, desired_output
