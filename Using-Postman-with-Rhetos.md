# Using Postman with the Rhetos framework
Create a collection for recording all requests. This will simplify and speed up testing of the application.
First choose the authentication type - NTLM, and enter your domain data (Image 1):

![Postman image 1](https://github.com/f-jovanovic/Rhetos-wiki/blob/master/images/postman-image1.png)

Image 2. shows the fields used for testing. Using the left dropdown menu you can select the desired HTTP method (GET, POST, PUT, PATCH, DELETE, etc.), while the center field should contain the server URL (server resource). When the desired method and URL have been entered, hit Send to send the request.
The difference between PUT and PATCH requests is that **PUT updates the whole entity** while **PATCH only updates the targeted property** of the entity.

![Postman image 2](https://github.com/f-jovanovic/Rhetos-wiki/blob/master/images/postman-image2.png)

If your request updates the database, then you should select *Body -> raw* and set the format to JSON (Image 3).

![Postman image 3](https://github.com/f-jovanovic/Rhetos-wiki/blob/master/images/postman-image3.png)

The request is sent to Rhetos through a REST service in the following format: `{Rhetos server URL}/Rest/{Module}/{Entity}/`
Image 3 shows accessing the SustavZaNabavu application through the REST service, and then accessing the module SustavZaNabavu and its entity Adresa. At the the end `/` needs to be added for accessing the resource: `http://localhost/sustavzanabavu/rest/sustavzanabavu/adresa/`

All the requests are sent in the JSON format. Date properties are formatted in the following way: `\/Date(YYYY-MM-DDTHH:MM:SS.sssZ)\/` (example: `"DatumPredaje":"\/Date(2017-10-31T23:00:00.000Z)\/"`)

Entities which contain references to other entities are stored in the database with an "ID" suffix and should be referenced in that way:
```
Entity Adresa
{
	ShortString Naziv{Required;}
	ShortString RedniBroj{Required;}
	Reference Grad SustavZaNabavu.Grad{Required;}
}
```
JSON:
```json
{
	"Naziv":"Gajeva",
	"RedniBroj":"122",
	"GradID":"af880d15-fd02-4e0c-b14f-889da06e2916"	
}
```

After saving in the database, the field below displays either an error or the ID of the newly created object.
```json
{
    "ID": "500a5a7c-a1cd-473d-885a-079dc4531bc0"
}
```

If we want to fetch the given address, the address ID needs to be added in the URL, wrapped in curly brackets, and the GET method need to be selected: `http://localhost/sustavzanabavu/rest/sustavzanabavu/adresa/{500a5a7c-a1cd-473d-885a-079dc4531bc0}`

When accessing specific data from the database, the URL should not end with `/`. When deleting from the database, you should select the DELETE method and send the ID of the wanted record in the JSON format. This ID also needs to be inserted in the REST request URL, wrapped in curly brackets. See image 4:

![Postman image 4](https://github.com/f-jovanovic/Rhetos-wiki/blob/master/images/postman-image4.png)

All of the errors can also be found in the `Rhetos/RhetosServer.log` file.
You should create all of the requests for test data insertion and save them in a collection. At the top of the Postman screen there is a Runner option which is used for running the whole collection at once.

## Method usage summary:

xxx = application URI
* GET: xxx/id
* POST: xxx/
* PUT: xxx/id
* DELETE: xxx/id

Rhetos REST API documentation: https://github.com/Rhetos/RestGenerator/blob/master/Readme.md