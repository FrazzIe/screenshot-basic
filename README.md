Forked from: https://github.com/citizenfx/screenshot-basic, https://github.com/gisleino/screenshot-basic, https://github.com/jonassvensson4/screenshot-basic

# screenshot-basic for FiveM

## Modified API
+ Added support to screenshot a selected area of the screen using the new arguments.
+ Added headers and imgur support

### Client

#### requestScreenshot(options?: any, cb: (result: string) => void)
Takes a screenshot and passes the data URI to a callback. Please don't send this through _any_ server events.

Arguments:
* **options**: An optional object containing options.
  * **encoding**: 'png' | 'jpg' | 'webp'.
  * **quality**: number - The quality for a lossy image encoder, in a range for 0.0-1.0. Defaults to 0.92.
  * **x**: number - Value of the X position.
  * **y**: number - Value of the Y position.
  * **w**: number - Widht of the screenshot.
  * **h**: number - Height of the screenshot.
* **cb**: A callback upon result.
  * **result**: A `base64` data URI for the image.

Example:

```lua
exports['screenshot-basic']:requestScreenshot({encoding = 'jpg', x = 0, y = 0, w = 1920, h = 1080}, function(data)
    TriggerEvent('chat:addMessage', { template = '<img src="{0}" style="max-width: 300px;" />', args = { data } })
end)
```

#### requestScreenshotUpload(url: string, field: string, options?: any, cb: (result: string) => void)
Takes a screenshot and uploads it as a file (`multipart/form-data`) to a remote HTTP URL.

Arguments:
* **url**: The URL to a file upload handler.
* **field**: The name for the form field to add the file to.
* **options**: An optional object containing options.
  * **encoding**: 'png' | 'jpg' | 'webp'.
  * **quality**: number - The quality for a lossy image encoder, in a range for 0.0-1.0. Defaults to 0.92.
  * **x**: number - Value of the X position.
  * **y**: number - Value of the Y position.
  * **w**: number - Widht of the screenshot.
  * **h**: number - Height of the screenshot.
  * **headers**: object - Request headers.
* **cb**: A callback upon result.
  * **result**: The response data for the remote URL.

Example:

```lua
exports['screenshot-basic']:requestScreenshotUpload('https://wew.wtf/upload.php', 'files[]', {encoding = 'jpg', x = 0, y = 0, w = 1920, h = 1080}, function(data)
    local resp = json.decode(data)
    TriggerEvent('chat:addMessage', { template = '<img src="{0}" style="max-width: 300px;" />', args = { resp.files[1].url } })
end)
```

### Server
The server can also request a client to take a screenshot and upload it to a built-in HTTP handler on the server.

Using this API on the server requires at least FiveM client version 1129160, and server pipeline 1011 or higher.

#### requestClientScreenshot(player: string | number, options: any, cb: (err: string | boolean, data: string) => void)
Requests the specified client to take a screenshot.

Arguments:
* **player**: The target player's player index.
* **options**: An object containing options.
  * **fileName**: string? - The file name on the server to save the image to. If not passed, the callback will get a data URI for the image data.
  * **encoding**: 'png' | 'jpg' | 'webp' - The target image encoding. Defaults to 'jpg'.
  * **quality**: number - The quality for a lossy image encoder, in a range for 0.0-1.0. Defaults to 0.92.
* **cb**: A callback upon result.
  * **err**: `false`, or an error string.
  * **data**: The local file name the upload was saved to, or the data URI for the image.


Example:
```lua
exports['screenshot-basic']:requestClientScreenshot(GetPlayers()[1], {
    fileName = 'cache/screenshot.jpg'
}, function(err, data)
    print('err', err)
    print('data', data)
end)
```

### Imgur headers examples

#### JavaScript
```javascript
// Imgur client ID
const CLIENT_ID = 'changeThis';

exports['screenshot-basic'].requestScreenshotUpload(`https://api.imgur.com/3/upload`, 'imgur', {
   headers: {
      'authorization': `Client-ID ${ CLIENT_ID }`,
      'content-type': 'multipart/form-data'
   }
}, ( data ) => {
   console.log(JSON.parse(data).data.link);
});
```
#### LUA
```lua
-- Imgur client ID
local CLIENT_ID = 'changeThis'

exports['screenshot-basic']:requestScreenshotUpload('https://api.imgur.com/3/upload', 'imgur', {
    headers = {
        ['authorization'] = string.format('Client-ID %s', CLIENT_ID),
        ['content-type'] = 'multipart/form-data'
    }
}, function(data)
   print(json.decode(data).data.link) 
end)
```
