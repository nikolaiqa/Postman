# Maps API collection

## Description:
### This collection imitates working with areas and places (e.g. buildings, parks, houses, etc) that could be add into a map app, update or remove. These actions are based on four basic requests, which are actually nothing more than standard CRUD methods: `POST`, `GET`, `PUT`, `DELETE` and each of them will be reviewed closely below. <br> Also at the end of description of methods it might be found an example of collection running from Postman client in GIF format

## Collection variables:

<table>
  <tbody>
    <tr>
      <th>VARIABLE</th>
      <th>INITIAL VALUE</th>
      <th>CURRENT VALUE</th>
      <th>Commentary</th>
    </tr>
    <tr>
      <td>URL</td>
      <td>https://rahulshettyacademy.com</td>
      <td>https://rahulshettyacademy.com</td>
      <td>Base URL for all requests, taken as a variable to simplify writing requests' URI</td>
    </tr>
    <tr>
      <td>place_id</td>
      <td>d965a5c91d7499b8b5e3620c392de4e2</td>
      <td></td>
      <td>The variable's value is set after <code>POST</code> request dynamically by scripts of Tests tab </td>
    </tr>
    <tr>
      <td>place_address</td>
      <td>Nemtsov street, 270215</td>
      <td>Nemtsov street, 270215</td>
      <td>The variable's value is constant and is needed for Tests tab of the <code>GET</code> request</td>
    </tr>
  </tbody>
</table>


## Methods:

## ```POST``` - **Add place** (*sending data of a new place through the request's body*)

### Base URL: https://rahulshettyacademy.com

### Endpoint: /maps/api/place/add/json

### Params

<table>
  <tbody>
    <tr>
      <th>KEY</th>
      <th>VALUE</th>
      <th>Commentary</th>
    </tr>
    <tr>
      <td>key</td>
      <td>qaclick123</td>
      <td>required param </td>
    </tr>
  </tbody>
</table>

### Example HTTP-request

```HTTP 
POST /maps/api/place/add/json?key=qaclick123 HTTP/1.1
Host: rahulshettyacademy.com
Content-Type: application/json
Content-Length: 308
```
###### Body raw (JSON)

```json
{
  "location": {
      "lat": -38.383494,
      "lng": 33.427362
  },
  "accuracy": 51,
  "name": "Frontline house",
  "phone_number": "(+91) 983 893 3937",
  "address": "Nemtsov street, 250215",
  "types": [
      "flat",
      "park"
  ],
  "website": "https://www.google.com",
  "language": "English"
}
```

### Example HTTP-response 

```json
{
    "status": "OK",
    "place_id": "d965a5c91d7499b8b5e3620c392de4e2",
    "scope": "APP",
    "reference": "63bf79956bbe23e03cb97ea40cef3fed63bf79956bbe23e03cb97ea40cef3fed",
    "id": "63bf79956bbe23e03cb97ea40cef3fed"
}
```

### Tests tab

```JS
let jData = pm.response.json(); // assign the http-response as the 'jData' variable

let placeID = jData.place_id; // assign the place_id value from the http-response as the 'placeID' variable (e.g. d965a5c91d7499b8b5e3620c392de4e2)

pm.collectionVariables.set("place_id", placeID); // set a collection variable with a value from the 'placeID' variable
```
<br>

---

<br>

## ```GET``` - **Check place** (*defining details of an added or updated place*)

### Base URL: https://rahulshettyacademy.com

### Endpoint: /maps/api/place/get/json

### Params

<table>
  <tbody>
    <tr>
      <th>KEY</th>
      <th>VALUE</th>
      <th>Commentary</th>
    </tr>
    <tr>
      <td>key</td>
      <td>qaclick123</td>
      <td>required param</td>
    </tr>
    <tr>
      <td>place_id</td>
      <td>{{place_id}} <br> (d965a5c91d7499b8b5e3620c392de4e2)</td>
      <td>required param that's taken from the collection variable, which is set from a response after POST-request</td>
    </tr>
  </tbody>
</table>

### Example HTTP-request

```HTTP 
GET /maps/api/place/get/json?key=qaclick123&place_id=d965a5c91d7499b8b5e3620c392de4e2 HTTP/1.1
Host: rahulshettyacademy.com
```

### Example HTTP-responses 

###### if everything's OK:

```json
{
    "location": {
        "latitude": "-38.383494",
        "longitude": "33.427362"
    },
    "accuracy": "51",
    "name": "Frontline house",
    "phone_number": "(+91) 983 893 3937",
    "address": "Nemtsov street, 250215",
    "types": "flat,park",
    "website": "http://google.com",
    "language": "English"
}
```
###### if not:

```json
{
    "msg": "Get operation failed, looks like place_id  doesn't exists"
}
```

### Tests tab

```javascript
let jData = pm.response.json(); // assign the http-response as the 'jData' variable
let address = jData.address; // assign the address value from the http-response as the 'address' variable
let exp_address = pm.collectionVariables.get("place_address"); // assign the coolection variable place_address as a 'exp_address'
let placeID = pm.collectionVariables.get("place_id"); // assign the coolection variable place_id as a 'placeID'

pm.test("Check address", function () {
    if (placeID != null){ // If the place_id exists
        if (address === exp_address){ // and if the current address matches the address, which was set as a collection variable
        console.log ('The address of the place is correct') &
        postman.setNextRequest('Update place'); // then next request should be 'Update place'
        } else console.log ('The address of the place was changed') &
        postman.setNextRequest('Delete place'); // if the addresses doesn't match then next request should be 'Delete place' 
    } else console.log ('The place was deleted') & 
    postman.setNextRequest(null); // If the place_id doesn't exist at all then the collection running must be stopped 
});  
```

<br>

---

<br>

## ```PUT``` - **Update place** (*allows to change only a place address through the request's body*)

### Base URL: https://rahulshettyacademy.com

### Endpoint: /maps/api/place/update/json

### Example HTTP-request

```HTTP 
PUT /maps/api/place/update/json HTTP/1.1
Host: rahulshettyacademy.com
Content-Type: application/json
Content-Length: 94
```
###### Body raw (JSON)

```json
{
    "key":"qaclick123",
    "place_id":"{{place_id}}", // It's taken from the collection variable, which was set after POST-response (e.g. d965a5c91d7499b8b5e3620c392de4e2) 
    "address":"Politkovskaya street, 071006"
}
```

### Example HTTP-responses 

###### if everything's OK:

```json
{
    "msg": "Address successfully updated"
}
```

###### if not:

```json
{
    "msg": "Update address operation failed, looks like the data doesn't exists"
}
```

### Tests tab

```javascript 
let jData = pm.response.json(); // assign the http-response as the 'jData' variable
let msg = jData.msg; // assign the msg value from the http-response as the 'msg' variable

pm.test("Check address", function () {
  if (msg === "Address successfully updated"){ // if the updating is successful 
      postman.setNextRequest('Check place')} // then the collection running jump to the 'Check place' request 
  else postman.setNextRequest(null); // if something's wrong then the collection running must be stopped
});
```
<br>

---

<br>

## ```DELETE``` - **Delete place** (*removing a place from the app's database*)

### Base URL: https://rahulshettyacademy.com

### Endpoint: /maps/api/place/delete/json

### Example HTTP-request

```HTTP 
DELETE /maps/api/place/delete/json HTTP/1.1
Host: rahulshettyacademy.com
Content-Type: application/json
Content-Length: 55
```
###### Body raw (JSON)

```json
{
    "place_id":"{{place_id}}" // It's taken from the collection variable, which was set after POST-response (e.g. d965a5c91d7499b8b5e3620c392de4e2) 
}
```

### Example HTTP-responses 

###### if everything's OK:

```json
{
    "status": "OK"
}
```

###### if not:

```json
{
    "msg": "Delete operation failed, looks like the data doesn't exists"
}
```

### Tests tab

```javascript 
let jData = pm.response.json(); // assign the http-response as the 'jData' variable
let status = jData.status; // assign the status value from the http-response as the 'status' variable

pm.test("Check status", function () {
    pm.expect(status).to.eql('OK'); // checking up whether the removing is successful  
});

pm.collectionVariables.set("place_id", null); // drop the collection variable

postman.setNextRequest('Check place'); // jump to the 'Check place' request to define that the place was truly deleted
```
<br>

---

## How the collection should run:
<p align="center">
<img  alt="Collection running" src="./Sourses/Collection_running.gif"/>
</p>

## To run the collection your own just copy the link below and past it in your Postman client (Import > Link):

### https://api.postman.com/collections/22139076-325a66ba-e6aa-44fd-ad2e-90ce7ff9b91c?access_key=PMAT-01GQ4ACPTN9VSMK106KECEP7JW
