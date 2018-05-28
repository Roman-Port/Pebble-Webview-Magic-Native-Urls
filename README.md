# Pebble-Webview-Magic-Native-Urls
This will show you how to call and use "magic urls" that allow you to do native functions in the Pebble webviews.

# Introduction
I've learned everything about these by looking at the source JavaScript for the appstore, so I figured that I should share it. 
You can see this in use on my appstore [here](https://pebble-appstore.romanport.com/assets/v2/PblNative.js).

The basic format of these URLs goes as follows...

The URL is sent in a GET request and it opened in a hidden iframe. 

| Example        | Notes        |   
| ------------- |:------------- |
| pebble-method-call-js-frame://      | This string will always be this. This is a fake protocol that the Pebble app will understand. **In Chrome, you will lose HTTPS by doing this**! |
| "" or "?" | This is the query character. It seems to be blank on Android, but it is a question mark on iOS. I'm not sure why they decided to have two options.
| method=setNavBarTitle      | "method=" will always remain the same. The text after this is the method type. See the "methods" table below for info. |
| &args=%7B"methodName"%3A"setNavBarTitle"%2C"callbackId"%3A2%2C "data"%3A"%7B%5C"title%5C"%3A%5C"Test%5C"%2C%5C "show_share%5C"%3A0%7D"%7D | This contains the URI-encoded JSON data that is used as as data for the arguemnts. This has it's own section below. (Keep in mind that this example has spaces to act as line breaks. These are not usually there.)|  

Here is an example produced by my code.
``pebble-method-call-js-frame://method=setNavBarTitle&args=%7B"methodName"%3A"setNavBarTitle"%2C"callbackId"%3A2%2C"data"%3A"%7B%5C"title%5C"%3A%5C"Test%5C"%2C%5C"show_share%5C"%3A0%7D"%7D``

# &args= And How To Use It
The &args= section of the URL is important. Without it, nothing will work.

The data is passed in using a uri-encoded stringify'd JSON string. This JSON will always be of the same format. This goes as follows...

| Key | Description | Example |
| --- |:----------- | ------- |
| methodName | This is the name of the request you'd like to make. Take a look at the methods section to see possible options. | "setNavBarTitle" |
| callbackId | This is the index of the callback in the list of callbacks stored in JS. Take a look at the Usage section for more. | 2 |
| data | This is the arguments for the method. These are sent in a JSON string (not inner JSON!) and are usually requred. Take a look at the example. | "{\"title\":\"Test\",\"show_share\":0}"}" |

Here is an example, first uri encoded, then raw.

``%7B"methodName"%3A"setNavBarTitle"%2C"callbackId"%3A2%2C"data"%3A"%7B%5C"title%5C"%3A%5C"Test%5C"%2C%5C"show_share%5C"%3A0%7D"%7D``

``{"methodName":"setNavBarTitle","callbackId":2,"data":"{\"title\":\"Test\",\"show_share\":0}"}``

Note: The data value is specific to setNavBarTitle and won't work for other methods. That'll be covered in the methods section.

# Methods
There are quite a few methods that appear in the Pebble appstore JavaScript. Some of them don't seem to do anything.

| Name (case sensitive) | Description | Data |
| --------------------- | ----------- | ---- |
| setNavBarTitle | Will set the bar on top of the screen. This can also toggle the share button with 1/0. | ``{"title":" [TITLE] ", "show_share": [INT 1/0] }`` |
| openURL | This will open a URL externally in the browser of choice. | ``{"url":" [URL] "}`` |
| addToLocker | I'd assume this adds the app to the locker, but not the device (like how the original Pebble had an 8 app limit). I've never used it. | See the app data section for this. |
| loadAppToDeviceAndLocker | This does the same as above, but pushes the app to the device. This is what I use to install apps. | See the app data section for this. |
| promptUserForAddToLockerOrLoad | I've never used this, but I'd assume that it asks the user if they'd like to put the app in their locker or the device. Useful for original Pebble. | *Unknown* |
| getAppsFromLocker | I'd assume this will get all apps from locker, but it has never worked for me. Let me know if you can get it working. | *Unknown* |
| removeFromLocker | I'd assume that you pass in an ID, and it will remove it from the locker. I don't know. | *Unknown* |
| isAppInLocker | I'd assume this will allow you to pass in an ID and check if it is in the locker. I've never been able to make this work. | *Unknown* |
| unloadAppFromPebble | I'd assume that this will unload the app from the original Pebble to free up a slot. I've never used it. | *Unknown* |
| getLoadedAppsFromPebble | I'd assume this checks if the app fills one of the original Pebble slots. | *Unknown* |
| tryWatchface | I don't know what this one does, but the title peaks my curiosity. | *Unknown* |
| isConnected | I'd assume that this checks if the watch is connected to the phone. | *Unknown* |
| closeScreen | I'd assume that this closes the webview and returns to the menu, but I've never been able to make it work. | *Unknown* |
| skipStep | *Unknown* | *Unknown* |
| bulkLoadAndClose | *Unknown* | *Unknown* |
| setVisableApp | This is used in the appstore to tell the Pebble app what is up front. It only seems to be used to give it the share link. | ``{"links":{"share":" [SHARE URL] "}}`` |
| refreshAccessToken | I'd assume this reloads the token, but I'm not sure why this would be needed by the WebView. | *Unknown* |

## App Data
This section will describe how to pass in app data for ``addToLocker`` and ``loadAppToDeviceAndLocker``.

| Key | Description | Example |
| --- | ----------- | ------- |
| id | The app ID. | ``561960c8a1dd2652af00000d`` |
| uuid | The app UUID. | ``70c08ef8-8c80-46ac-83f7-1af4b43949cb`` |
| title | The app name. | ``Snowy`` |
| list_image | The URL to the icon that will appear on the apps list. | ``%rootUrl%/files/561960c8a1dd2652af00000d/list.png`` |
| icon_image | The URL to the icon. Watchfaces will never have these. | ``%rootUrl%/files/561960c8a1dd2652af00000d/icon.png`` |
| screenshot_image | The URL to the first screenshot. | ``%rootUrl%/files/561960c8a1dd2652af00000d/screenshot_0.png`` |
| type | The type of app. This'll usually be ``watchface`` or ``watchapp``. | ``watchapp`` |
| pbw_file | The all-important URL to the pbw file to install. | ``%rootUrl%/files/561960c8a1dd2652af00000d/app.pbw`` |
| links | A inner-JSON element that has paths to some links. | *View the table below* |

Here is the table for the links section.

| Key | Description | Example |
| --- | ----------- | ------- |
| add | This URL is used when the app is added. Replace the ID in this url with the app's ID. I haven't replaced this yet in my appstore. | ``https://dev-portal.getpebble.com/api/applications/561960c8a1dd2652af00000d/add`` |
| remove | This URL is used when the app is removed. Replace the ID in this url with the app's ID. I haven't replaced this yet in my appstore. | ``https://dev-portal.getpebble.com/api/applications/561960c8a1dd2652af00000d/remove`` |
| share | This URL is what will be used when the user shares this app. Replace the ID in this url with the app's ID. | ``https://pebble-appstore.romanport.com/app/561960c8a1dd2652af00000d`` |

### App Data Example
Here is an example of the appdata.
```json
{
   "id":"561960c8a1dd2652af00000d",
   "uuid":"70c08ef8-8c80-46ac-83f7-1af4b43949cb",
   "title":"Snowy",
   "list_image":"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/list.png",
   "icon_image":"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/icon.png",
   "screenshot_image":"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/screenshot_0.png",
   "type":"watchapp",
   "pbw_file":"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/app.pbw",
   "links":{
      "add":"https://dev-portal.getpebble.com/api/applications/561960c8a1dd2652af00000d/add",
      "remove":"https://dev-portal.getpebble.com/api/applications/561960c8a1dd2652af00000d/remove",
      "share":"https://pebble-appstore.romanport.com/app/561960c8a1dd2652af00000d"
   }
}
```
Here is some example JavaScript code that will create the data. (Don't worry about ``DecodeJSONUrlRequest`` and ``GetPathToRoot``. They just replaces some things for my specific use case. )

```javascript
var args = {
	id: pbwJSON.id, 
	uuid: pbwJSON.uuid, 
	title: pbwJSON.name,
	list_image: DecodeJSONUrlRequest(pbwJSON.path_list),
	icon_image: DecodeJSONUrlRequest(pbwJSON.path_icon),
	screenshot_image: DecodeJSONUrlRequest(pbwJSON.path_screenshots[0]),
	type: pbwJSON.appType,
	pbw_file: DecodeJSONUrlRequest(pbwJSON.path_pbw),
	links: {
		add: 'https://dev-portal.getpebble.com/api' + "/applications/" + pbwJSON.id + "/add",
		remove: 'https://dev-portal.getpebble.com/api' + "/applications/" + pbwJSON.id + "/remove",
		share: GetPathToRoot() + "app/" + pbwJSON.id
	}
};
```

# Usage
Setting up and using the native functions takes some work, but is easy to use. This guide will set up a simple system. Take a look at my JavaScript file for more.

To begin, we'll need to create some global variables.

```javascript
var callbackId=0;
var callbacks = [];
```

The ``callbacks`` list is added to every time a native call is made. The ``callbackId`` variable is used so we know where our newest entry is.

## Usage - encodeMsg

We'll start on the lowest-level function and build up from there. We'llstart with the ``encodeMsg`` function. This will take in the method name, the callback ID, and the args. We'll call this in the next function, but we'll make it now.
```javascript
function encodeMsg(methodName, callbackId, args) {
	var msgStringified, msg = {
		methodName: methodName,
		callbackId: callbackId,
		data: args
	};
	try {
		msgStringified = JSON.stringify(msg)
	} catch (e) {
		alert("[ERROR Native] msg cannot be JSON encoded", e)
	}
	var msgURIEncoded;
	try {
		msgURIEncoded = encodeURIComponent(msgStringified)
	} catch (e) {
		alert("[ERROR Native] msg cannot be URI encoded", e)
	}
	return msgURIEncoded;
}
```

### Usage - encodeMsg - Explanation

This function is way too long. It essentially creates the JSON object that will be sent, stringifies it, and URI encodes it.
We create the JSON object here (**don't copy it, use the one above instead**)
```javascript
var msg = {
	methodName: methodName,
	callbackId: callbackId,
	data: args
};
```

This follows the form used in the &args= function.

Next, stringify it. 
``msgStringified = JSON.stringify(msg);``

Now, URI encode it.
``msgURIEncoded = encodeURIComponent(msgStringified)``

Then, return it.
``return msgURIEncoded;``

## Usage - buildURI
This function is what will actually create the URL that will be opened.

```javascript
function buildURI(methodName, _callbackId, args) {
	var msg = encodeMsg(methodName, _callbackId, args)
	  , protocol = "pebble-method-call-js-frame://"
	  , queryCharacter = platformAndroid === false ? "?" : ""
    , uri = protocol + queryCharacter + "method=" + methodName + "&args=" + msg;
	return uri;
}
```

### Usage - buildURI - Explanation
This function is a pretty simple function that appends strings. It will choose the query character ("" on Android, "?" on iOS), and add the url arguments.

## Usage - send
This is the main function you'd like to call. It will create the callbacks and open the iframe.

```javascript
function send(methodName, args, responseCallback, sendCallback)  {
	var _callbackId = -1; 
	if(typeof(responseCallback)=="function") {
		//Push callback
		callbacks.push(responseCallback);
		_callbackId = callbackId;
		callbackId+=1;
	}
	
	
	//Create iframe
	var uri = buildURI(methodName,_callbackId,args);
	var iframe = document.createElement("iframe");
	iframe.setAttribute("src", uri),
	iframe.setAttribute("height", "100px"),
	iframe.setAttribute("width", "1px"),
	document.documentElement.appendChild(iframe),
	iframe.parentNode.removeChild(iframe),
	iframe = null
}
```

### Usage - send - Explanation
The send function is made with two parts. The first part adds the callback, and the second part creates the url and makes the iframe.

This part will push the callback to the list of callbacks, create a variable with the callback id, then increment the callbackID global variable.

```javascript
var _callbackId = -1; 
if(typeof(responseCallback)=="function") {
	//Push callback
	callbacks.push(responseCallback);
	_callbackId = callbackId;
	callbackId+=1;
}
```

The next part will create the url to use.
``var uri = buildURI(methodName,_callbackId,args);``

Next, it'll create an iframe and set the source of it to the url. After that, it will be removed.

```javascript
var uri = buildURI(methodName,_callbackId,args);
var iframe = document.createElement("iframe");
iframe.setAttribute("src", uri),
iframe.setAttribute("height", "100px"),
iframe.setAttribute("width", "1px"),
document.documentElement.appendChild(iframe),
iframe.parentNode.removeChild(iframe),
iframe = null
```

# Examples
Here is an example URL that will change the window title.

URL: ``pebble-method-call-js-frame://method=setNavBarTitle&args=%7B%22methodName%22%3A%22setNavBarTitle%22%2C%22callbackId%22%3A0%2C%22data%22%3A%22%7B%5C%22title%5C%22%3A%5C%22Test%5C%22%2C%5C%22show_share%5C%22%3A0%7D%22%7D``

JSON encoded: ``{"methodName":"setNavBarTitle","callbackId":0,"data":"{\"title\":\"Test\",\"show_share\":0}"}``

Here is an example that will install an app to the phone.

URL: ```pebble-method-call-js-frame://method=loadAppToDeviceAndLocker&args=%7B%22methodName%22%3A%22loadAppToDeviceAndLocker%22%2C%22callbackId%22%3A0%2C%22data%22%3A%22%7B%5C%22id%5C%22%3A%5C%22561960c8a1dd2652af00000d%5C%22%2C%5C%22uuid%5C%22%3A%5C%2270c08ef8-8c80-46ac-83f7-1af4b43949cb%5C%22%2C%5C%22title%5C%22%3A%5C%22Snowy%5C%22%2C%5C%22list_image%5C%22%3A%5C%22https%3A%2F%2Fpebble-appstore.romanport.com%2Ffiles%2F561960c8a1dd2652af00000d%2Flist.png%5C%22%2C%5C%22icon_image%5C%22%3A%5C%22https%3A%2F%2Fpebble-appstore.romanport.com%2Ffiles%2F561960c8a1dd2652af00000d%2Ficon.png%5C%22%2C%5C%22screenshot_image%5C%22%3A%5C%22https%3A%2F%2Fpebble-appstore.romanport.com%2Ffiles%2F561960c8a1dd2652af00000d%2Fscreenshot_0.png%5C%22%2C%5C%22type%5C%22%3A%5C%22watchapp%5C%22%2C%5C%22pbw_file%5C%22%3A%5C%22https%3A%2F%2Fpebble-appstore.romanport.com%2Ffiles%2F561960c8a1dd2652af00000d%2Fapp.pbw%5C%22%2C%5C%22links%5C%22%3A%7B%5C%22add%5C%22%3A%5C%22https%3A%2F%2Fdev-portal.getpebble.com%2Fapi%2Fapplications%2F561960c8a1dd2652af00000d%2Fadd%5C%22%2C%5C%22remove%5C%22%3A%5C%22https%3A%2F%2Fdev-portal.getpebble.com%2Fapi%2Fapplications%2F561960c8a1dd2652af00000d%2Fremove%5C%22%2C%5C%22share%5C%22%3A%5C%22https%3A%2F%2Fpebble-appstore.romanport.com%2Fapp%2F561960c8a1dd2652af00000d%5C%22%7D%7D%22%7D```

JSON encoded: ```{"methodName":"loadAppToDeviceAndLocker","callbackId":0,"data":"{\"id\":\"561960c8a1dd2652af00000d\",\"uuid\":\"70c08ef8-8c80-46ac-83f7-1af4b43949cb\",\"title\":\"Snowy\",\"list_image\":\"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/list.png\",\"icon_image\":\"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/icon.png\",\"screenshot_image\":\"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/screenshot_0.png\",\"type\":\"watchapp\",\"pbw_file\":\"https://pebble-appstore.romanport.com/files/561960c8a1dd2652af00000d/app.pbw\",\"links\":{\"add\":\"https://dev-portal.getpebble.com/api/applications/561960c8a1dd2652af00000d/add\",\"remove\":\"https://dev-portal.getpebble.com/api/applications/561960c8a1dd2652af00000d/remove\",\"share\":\"https://pebble-appstore.romanport.com/app/561960c8a1dd2652af00000d\"}}"}```
