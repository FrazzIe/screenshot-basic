# screenshot-basic for FiveM
OG repo : https://github.com/citizenfx/screenshot-basic
## Modified API
+ Added support to screenshot a selected area of the screen using the new arguments.

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
* **cb**: A callback upon result.
  * **result**: The response data for the remote URL.

Example:

```lua
exports['screenshot-basic']:requestScreenshotUpload('https://wew.wtf/upload.php', 'files[]', {encoding = 'jpg', x = 0, y = 0, w = 1920, h = 1080}, function(data)
    local resp = json.decode(data)
    TriggerEvent('chat:addMessage', { template = '<img src="{0}" style="max-width: 300px;" />', args = { resp.files[1].url } })
end)
```
