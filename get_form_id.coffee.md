# Form ID

There are separate form ID values for an object definition and a placed instance of that object.

You can view the form ID of the placed instance in game by opening the console (press the back-tick) then clicking something.

The first 2 digits (source mod index) is the number of the mod load order.
Objects from the base module (first loaded) will be `00`.
The second-loaded mod will use `01`.

Objects belonging to a save gave will use `FF`.
Instances can be quite common, like from generated items.


## Exports

	module.exports = (state)->
		new Promise (resolve, reject)->
			if state.buffer.length < 8
				reject 'Missing data for form ID.'

			else
				#source_mod = state.buffer.slice 0, 2
				#object_index = state.buffer.slice 2, 8

				resolve state.buffer.readUInt32LE 0, 8


## Links

- [FormID @ UESP](http://www.uesp.net/wiki/Tes4Mod:Formid)
