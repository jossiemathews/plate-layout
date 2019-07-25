# JavaScript Plate Layout
JavaScript Plate Layout is an open source tool developed collaboratively by [Chai Biotechnologies](www.chaibio.com) and [New England BioLabs](www.neb.com) for visualizing and editing the layout of scientific assay plates.

Many scientific instruments such as PCR thermocyclers, DNA sequencers, and microplate readers use plates ranging from 24 to 384 wells, with 96 well plates being particularly common. It is usually necessary to set data attributes for each of the wells, both so that the instrument can properly configure itself, and so that results can be properly analyzed. Correctness of the layout is critical for the integrity of results, but not always easy to obtain given the number of wells and data attributes to be assigned.

JavaScript Plate Layout provides a tool for visualizing the plate layout using a few dimensions at a time, to better comprehend the layout they have created. It provides extensive plate editing capabilities and is designed to be easily utilized in the context of a larger scientific software application.

## Features
* Assign and edit up to roughly 25 data attributes to plates ranging from 16 to 96 wells
* Incrementally save plate layouts to server via JavaScript callback interface
* Colorfully visualize the layout using user-selected data dimensions
* Assign attributes to multiple wells at once
* Supports multiple units of measure for numeric attributes
* Undo / redo support
* Import plate templates
* Plate and well completion status indication

## Usage
Embed code similar to the below to add the plate layout tool to your application. See Configuration Options for all available settings.

```html
<head>
	<script type="text/javascript" src="javascripts/plate-layout.js"></script>
	<script type="text/javascript">
		var volume_units = [
			{
				id:   'unit1',
				name: 'uL'
			},
			{
				id:   'unit2',
				name: 'mL'
			}
		];

		var attributes = [
			//tab 1
			[
				{
					id:       'volume',
					name:     'Volume',
					type:     'numeric',
					units:    volume_units //optional
				},
				//etc
			],

			//tab 2
			[
				{
					id:         'pol',
					name:       'Polymerase',
					type:       'multiselect',
					options: [
						{
							id:   '234',
							name: 'Taq 1'
						},
						{
							id:   '123',
							name: 'Taq 2'
						}
					]
				}
			]; //etc, up to 4 tabs
		];

		$("#my-plate-layout").plateLayout({

			numRows:          '8',
			numCols:          '12',
			attributes:       attributes,

			getPlate: function() {
				//this function should retrieve the plate from the server
				//and call either getPlateSuccessful() or getPlateFailed()
				//on completion
			},

			updateWells: function(wells) {
				//this function should save the provided wells to the server
				//and call either updateWellsSuccessful() or updateWellsFailed()
				//on completion
			}
		});
	</script>
</head>

<body>
    <div id="my-plate-layout"></div>
</body>
```

## Attribute Specification
More detailed explanation of the example attribute definition provided above to come.

## JavaScript API
### User-Provided Callback Functions
The following two callback functions must be implemented by the user and provided to the init function.

#### updateWells(wellChangeArray)
As the user makes changes, the plate layout tool tracks what changes need to be saved to the server. It serializes these changes into batches, and calls this function once per batch. The
function is passed a list of wells that have changes, and should make an AJAX request to save these changes to the server. Upon completion you should call either `plateLayout.updateWellsSuccessful()` or `plateLayout.updateWellsFailure()`. If successful, updateWells() will be called again once there are further changes. If a failure occurs, an error will be displayed to the user, and the plate will be re-loaded.

#### getPlate()
This function is called when the tool initializes to retrieve the existing plate layout. Once retrieved, you should call either `plateLayout.getPlateSuccessful(wells)` or `plateLayout.getPlateFailed()`. If successful, well data should be passed in the following form:

```
[
	//well 0
	{
		attribute_id_1: 'attribute_value_1',
		attribute_id_2: 'attribute_value_2
	},

	//well 1, etc
]
```
`attribute_id_x` cooresponds to the id of the attribute as it is defined in the configuration options.

###Plate Layout Callback Functions

The plate layout tool exposes the following callback functions intended to be called by the user.

#### getPlateSuccessful(wells)
Called back in response to getPlate(). Used to initialize the plate layout tool with the well array data if getPlate() succeeded.

#### getPlateFailed()
Called back in response to a failed getPlate() call.

#### updateWellsSuccessful()
Called back when updateWells() successfully updates all wells it was provided to the server.

#### updateWellsFailed()
Called back when updateWells() was unsuccessful in updating all wells to the server.

###Data Types.

We have four data types which can be used to initialize tabs in the right hand side. They are text, numeric, boolean and multichoice.

#### Text

Text field are the normal and basic text field they are just take some text value inside. Nothing specific.

#### Numeric

When we have numeric data which has some unit we use numeric data type. Sometime we should be using volume like quantities, So we provide an extra field to hand over units too.

```
Speed: {
	id:       'SpeedData',
	name:     'Speed',
	type:     'numeric',
	placeholder: "Speed",

	units: {
		1: "m/s",
		2: "km/hr"
	}
}

```

see the units in the above object. Units will be a seperate dropdown and will be placed over the text box where we enter speed data.

#### Boolean Field

Name says it all, Just brought the select2 to show it.

#### Multiselect

Normal select box but we bring select2 to modify it. Look at the example object here.

```
Polymerase: {
	id: 'pol',
	name: 'Polymerase',
	type: 'multiselect',
	placeHolder: "Polymerase",

	options: {
			'Taq 1':  {
						id:   '234',
						name: 'Taq 1'
				},
			'Taq 2':  {
						id:   '123',
						name: 'Taq 2'
				}
	}
}

```

Here options are going to be the values in the dropdown.
