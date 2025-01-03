About SimpleResponse
This tutorial is extended from Part #3 Initializing Server Configuration.

SimpleResponse.Enable = False
By default, a predefined structure is return when using ReturnHttpResponse sub of WebApiUtils to output the JSON response.
I recommend to use the default because it covers the response status code, message and error message without the need to worry whether the response should be an array or object. The response data is always returned as an array (list).
Here is an example of the output:
JSON:
{
  "a": 200,
  "r": [
    {
      "deleted_date": null,
      "category_id": 2,
      "id": 3,
      "product_price": 1000,
      "created_date": "2024-10-30 08:33:13",
      "product_code": "T002",
      "modified_date": null,
      "product_name": "Optimus Prime"
    }
  ],
  "s": "ok",
  "e": null,
  "m": "Success"
}
You won't notice this format in API documentation because only the response 'r' item is show. A small label is display below the response box results from the combination of 'a' and 'm' / 'e' items.
For details, you can check the JavaScript code of help.js file inside scripts folder of www/assets.
JavaScript:
success: function (data) {
    if (data.s == "ok" || data.s == "success") {
        var content = JSON.stringify(data.r, undefined, 2)
        $("#response" + id).val(content)
        $("#alert" + id).html(data.a + ' ' + data.m)
        $("#alert" + id).removeClass("alert-danger")
        $("#alert" + id).addClass("alert-success")
        $("#alert" + id).fadeIn()
    }
    else {
        var content = JSON.stringify(data.r, undefined, 2)
        $("#response" + id).val(content)
        $("#alert" + id).html(data.a + ' ' + data.e)
        $("#alert" + id).removeClass("alert-success")
        $("#alert" + id).addClass("alert-danger")
        $("#alert" + id).fadeIn()
    }
}
SimpleResponse.Enable = True
You will notice in this version, the setting is set to True inside the code.
B4X:
Config.SimpleResponse.Enable = True
Note: You can comment the code if you want.
This is the case where you want to create API that follow the structure you want.
When this setting is set to True, the JSON format as showed above is now show with just the 'r' part, exactly as what you see in the Response text area in API documentation page.
JSON:
{
    "deleted_date": null,
    "category_id": 2,
    "id": 3,
    "product_price": 1000,
    "created_date": "2024-10-30 08:33:13",
    "product_code": "T002",
    "modified_date": null,
    "product_name": "Optimus Prime"
}
You can actually compare the difference by viewing the actual output by pasting the API end point to the browser address.
Take note that you can only see the output for GET request in browser.
Using browser
Debugging using Developer Tools
We can use the Developer Tool in web browser for debugging the API response.
First we need to write console.log() inside the JavaScript for AJAX call.
This is useful for debugging other methods such as POST, PUT and DELETE.
When Config.SimpleResponse.Enable = True, the response is in simple format.
The page for API documentation will load help.simple.js file to make AJAX calls.
Open help.simple.js using a Text Editor and add the following code at line #88.
console.log(data)
The code will look like the following:
JavaScript:
case element.hasClass("get"):
    return {
        type: "GET",
        headers: headers,
        success: function (data, textStatus, xhr) {
            console.log(data)
            var content = JSON.stringify(data, undefined, 2)
            $("#response" + id).val(content)
            $("#alert" + id).html(xhr.status + ' ' + textStatus)
            $("#alert" + id).removeClass("alert-danger")
            $("#alert" + id).addClass("alert-success")
            $("#alert" + id).fadeIn()
        },
        error: function (xhr, textStatus, errorThrown) {
            var content = xhr.responseText
            $("#response" + id).val(content)
            $("#alert" + id).html(xhr.status + ' ' + errorThrown)
            $("#alert" + id).removeClass("alert-success")
            $("#alert" + id).addClass("alert-danger")
            $("#alert" + id).fadeIn()
        }
    }
    break
After changes is saved, you need to refresh the JavaScript source by right clicking on the page and choose view page source (Ctrl+U).
Scroll down to the bottom of the page until you see
<script src="http://127.0.0.1:8080/assets/scripts/help.simple.js"></script>
Click on the link to open it on a new tab and right-click Reload (Ctrl+R).
Open the Dev Tools by right clicking on the page (on Google Chrome) and choose Inspect (usually you need to scroll to bottom of the pop up menu) or just press combo keys of Ctrl+Shift+I.
The Dev Tools panel will appear. Usually it is focusing on the Console tab. Otherwise you need to click on the Console tab.
Now try to make a new GET request.
You should see an arrow with text Object appeared and on the left showing 1 user message.
On the right you see the name of the JavaScript file colon the line number on the code that produce the message log.
Spoiler: Web Developer Tools
Click the Object to expand it. You can see the content of the JSON object return from the API.
Spoiler: Show Object
If it is not working, probably you need to restart the server. Stop the debug and click run again. Then try again.
The default JSON Format for SimpleResponse is "Auto".
You can change the Format by setting it to "Map" to return as object (or "List" to return as array) for all the API responses.
SimpleResponse.Format
By changing it to Map, you need to specify the key of the "Map" using the DataKey property.
If you don't specify a key name, it will use "data" as the default key.
B4X:
Config.SimpleResponse.Initialize
Config.SimpleResponse.Enable = True
Config.SimpleResponse.Format = "Map"
Config.SimpleResponse.DataKey = "result"
After made this changes, now try to restart the debug (F11) and see the difference of output but this time we test on the API end point that should return an array (list) which is the /api/products end point.
Spoiler: SimpleResponse.Format = "Map"
So you see the response is now return as object (wrapped in curly brackets) instead as an array (wrapped in square brackets) and it is using the key that we specified.
Conclusion
There are 4 types of output for SimpleResponse:
Enable	Format	Return data
False	(Fixed)	object with r data always an array
True	Auto (default)	depends whether we initialize object or data
True	Map	always as object (with optional customize key name)
True	List	always as array
It depends on your use case which type is suitable.
You can build more complex JSON response by nesting the objects and arrays.

[end of tutorial]