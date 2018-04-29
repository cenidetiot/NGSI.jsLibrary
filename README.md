Welcome to NGSI-Parser!
===================


ngsi-parser is a npm module for parsing and converting a simple JSON or value to a NSGI-compliant object

----------

* [Data type suported](#usage)
* [How to Install](#how-to-install)
* [Usage](#usage)
	* [Import](#import)
	* [Entities Functions](#entities-functions)
		* [Parse an Entity](#parse-an-entity)
		* [Parse an Attribute ](#parse-an-attribute)
		* [Parse a value](#parse-a-value)
	* [Usage with ocb-sender](#usage-with-ocb-sender)
	* [Special Consults](#special-consults)
		* [Dinamic Query Consult](#dinamic-query-consult)
	* [Data Model JSON Schema Analizer](#data-model-json-schema-analizer)
	
* [License](#license)

## Data types suported

If value is a **string**, then type **Text** is used.
If value is a **number**, then type **Number** is used.
If value is a **boolean**, then type **Boolean** is used.
If value is **Date**, then **DateTime** is used.
If value is an **object** or **array**, then **StructuredValue** is used.
If value is **null**, then **None** is used.

## How to Install


    npm install ngsi-parser 
    
   or
   
	 yarn add ngsi-parser



## Import

#### ES5 

```js
	var ngsi = require('ngsi-parser')
```

#### ES6 
	

```js
	import NGSI as ngsi from 'ngsi-parser'
```

	

## Entities Functions


#### Parse an Entity

```js
	var entity = ngsi.parseEntity({
		id :'Room1',
		type:'Room',
		temperature : {
			value : 50 ,
			metadata : {
				frecuency: 40,
				scale: 'Celsious'
			}
		},
		dateStamp : new Date()
	})
```
 
Output

```json
	{
		"id":"Room1",
		"type":"Room",
		"temperature":{
			"value":50,
			"type":"Number",
			"metadata":{
				"frecuency":{
					"value":40,
					"type":"Number"
				},
				"scale":{
					"value":"Celsious",
					"type":"Text"
				}
			}
		},
		"dateStamp":{
			"value":"2017-10-08T04:01:19.560Z",
			"type":"DateTime",
			"metadata":{}
		}
	}

```
#### Parse an Attribute

```js
	var attribute = ngsi.parseAttrs({
		temperature : {
			value : 50,
			metadata :{
				frecuency : 50,
				scale: 'Fahrenheit'
			}
		}
	})
```


Output

```json
	{
		"temperature":{
			"value":50,
			"type":"Number",
			"metadata":{
				"frecuency":{
					"value":50,
					"type":"Number"
				},
				"scale":{
					"value":"Fahrenheit",
					"type":"Text"
				}
			}
		}
	}

```


#### Parse a Value

```js
	var value = ngsi.parseValue(50)
```

Output

```json
	{
		 "value": 50,
		 "type": "Number",
		 "metadata": {}
	 }

```

## Usage with ocb-sender

For more information about [ocb-sender](https://github.com/cenidetiot/OCB.jsLibrary) see its documentation.

#### Create an Entity in the ContextBroker
```js
	//Convert a Json to Ngsi
	var entity = ngsi.parseEntity({
		id :'Room1',
		type:'Room',
		temperature : {
			value : 50 ,
			metadata : {
				frecuency: 40,
				scale: 'Celsious'
			}
		},
		dateStamp : new Date()
	})
	// Send to ContextBroker 
	cb.createEntity(entity)
	.then((result) => console.log(result))
	.catch((err) => console.log(err))
```

#### Update all the object attributes of an entity
```js
	//Convert a Json to Ngsi
	var attribute = ngsi.parseAttrs({
		temperature : {
			value : 50
		}
	})
	// Send to ContextBroker 
	cb.updateEntityAttrs('Room1', attribute)
	.then((result) => console.log(result))
	.catch((err) => console.log(err))
```

#### Add a JSON Attribute to a NGSI entity.
```js
	//Convert a Json to Ngsi
	var attribute = ngsi.parseAttrs({
		temperature : {
			value : 50
		}
	})
	// Send to ContextBroker 
	cb.addJSONAttributeToEntity('Room1', attribute)
	.then((result) => console.log(result))
	.catch((err) => console.log(err))
```


#### Update the JSON Object of an atttribute of the entity
```js
	//Convert a Json to Ngsi
	var value = ngsi.parseValue(50)
	
	// Send to ContextBroker 
	cb.updateJSONAttrEntity(
		'idEntity', 'nameAttribute', value
	)
	.then((result) => console.log(result))
	.catch((err) => console.log(err))
```

## Special Consults

### Dinamic Query Consult
```js
	//Convert a Json to Query
	let query = ngsi.createQuery({
	"id":"Device.*",
	"type":"Device",
	"options":"keyValues",
	"dateObserved" : ">=2018-02-20T16:54:03.931-06:00"
	})
	console.log(query)
```
Output

```text
	?id=Device.*&Device&type=Device&options=keyValues&q=dateObserved>=2018-02-20T16:54:03.931-06:00
```
Usage with OCB-sender

```js
	//Send requests to ContextBroker
	cb.getWithQuery(query)
    .then((result) => console.log(result))
	.catch((err) => console.log(err))
```

## Data Model JSON Schema Analizer

ngsi-parser helps you to manage your Data Models like used in Fiware, ngsi-parser can analize if the entity complies with the specified model and identify its errors, to do it you need the JSON Schema of this Data Model provided or you can build some.

You can know about JSON Schemas in  [JSON Schema](http://json-schema.org/).

### Importing your JSON Schema

You  can import your JSON schema importing a JSON from a file or from a repository using the help of ocb-sender.

#### Importing From a  JSON File
```javascript
	var ngsi = require('ngsi-parser');
	var mySchema = require('mySchema.json');
	ngsi.setModel({
		mySchema : mySchema
	});
```
#### Importing From a remote repository
```javascript
	var ngsi = require('ngsi-parser');
	ngsi.setModel({
		myRemoteSchema : 'https://yourdatamodels.com/myRemote'
	});
```
#### Importing from various sources 

```javascript
	var ngsi = require('ngsi-parser');
	var mySchema = require('mySchema.json');
	ngsi.setModel({
		mySchema : mySchema,
		myRemoteSchema : 'https://yourdatamodels.com/myRemote',
		AlertModel : 'https://raw.githubusercontent.com/smartsdk/dataModels/master/Alert/schema.json',
		anotherModel : require('anotherSchema.json')
	});
```


#### Using your Data Models Schemas 
To use the schemas imported from a JSON file only you need to specify the name with which you entered it to ngsi-parser and it will return you one array with the errors found.
```javascript
	var ngsi = require('ngsi-parser');
	var mySchema = require('mySchema.json');
	ngsi.setModel({
		mySchema : mySchema
	});
	var entity = { 
		id :'Room1',
		type:'Room',
		temperature : 50, 
		dateStamp :  new  Date()  
	};
	let errors  = ngsi.verifyModel('mySchema', entity);
	if (errors.length === 0 ){
		console.log("The entity it's OK")
	}else {
		errors.map(console.log)
	}
```
To use Schemas from a remote repository is necesary download it, is because you need use ocb-sender, and in this case the method ngsi.verifyModel() becomes to a promise.
```javascript
	var ngsi = require('ngsi-parser');
	var ocb = require('ocb-sender');
	ngsi.setModel({
		myRemoteSchema : 'https://yourdatamodels.com/myRemote',
	});
	var entity = { 
		id :'Room1',
		type:'Room',
		temperature : 50, 
		dateStamp :  new  Date()  
	};
	ngsi.verifyModel('myRemoteSchema', entity, ocb)
	.then((errors) => { 
		if (errors.length === 0 ){
			console.log("The entity it's OK")
		}else {
			errors.map(console.log)
		}
	})
```
#### Use without storing the JSON schema 
```javascript
	var ngsi = require('ngsi-parser');
	var ocb = require('ocb-sender');
	ngsi.verifyModel('https://yourdatamodels.com/myRemote', entity ,cb)
	.then((errors) => { 
		if (errors.length === 0 ){
			console.log("The entity it's OK")
		}else {
			errors.map(console.log)
		}
	})
```




#### License 

MIT &copy; Daniel Torres & Haidée Onofre
