# gifuct-js
A Simple to use javascript .GIF decoder.

We needed to be able to efficiently load and manipulate GIF files for the **[Ruffle][1]** hybrid app (for mobiles). There are a couple of example libraries out there like [jsgif][2] & its derivative [libgif-js][3], however these are admittedly inefficient, and a mess. After pulling our hair out trying to understand the ancient, mystic gif format (hence the project name), we decided to just build our own. This library also removes any specific drawing code, and simply parses, and decompresses gif files so that you can manipulate and display them however you like.

### Demo

You can see a demo of this library in action **[here][4]**

### Usage

*Installation:*

    bower install --save gifuct-js
    
*Decoding:*

This decoder uses **[js-binary-schema-parser][5]** to parse the gif files (you can examine the schema in the source). This means the gif file must firstly be converted into a `Uint8Array` buffer in order to decode it. Some examples:

* *Angularjs*


        return $http.get(gifURL, {
            responseType: 'arraybuffer'
        }).then(function(result){
            var gif = new GIF(result.data);
            // return the frame data as the promise result
            return gif.decompressFrames();
        });
    
* *XMLHttpRequest*
    
     
        var oReq = new XMLHttpRequest();
        oReq.open("GET", gifURL, true);
        oReq.responseType = "arraybuffer";

        oReq.onload = function (oEvent) {
            var arrayBuffer = oReq.response; // Note: not oReq.responseText
            if (arrayBuffer) {
                var byteArray = new Uint8Array(arrayBuffer);
                var gif = new GIF(byteArray);
                var frames = gif.decompressFrames();
                // do something with the frame data
            }
        };

        oReq.send(null);

*Result:*

The result of the `decompressFrames()` function returns an array of all the GIF image frames, and their meta data. Here is a an example frame:

    {
    	// The color table lookup index for each pixel
        pixels: [...], 
        // the dimensions of the gif frame (see disposal method)
        dims: { 
            top: 0,
            left: 10,
            width: 100,
            height: 50
        },
        // the time in milliseconds that this frame should be shown
        delay: 50,
        // the disposal method (see below)
        disposalType: 1,
        // an array of colors that the pixel data points to
        colorTable: [...],
        // An optional color index that represents transparency (see below)
        transparentIndex: 33
     }
     
 *Disposal Method:*
 
The `pixel` data is stored as a list of indexes for each pixel. These each point to a value in the `colorTable` array, which contain the color that each pixel should be drawn. Each frame of the gif may not be the full size, but instead a patch that needs to be drawn over a particular location. The `disposalType` defines how that patch should be drawn over the gif canvas. In most cases, that value will be `1`, indicating that the gif frame should be simply drawn over the existing gif canvas without altering any pixels outside the frames patch dimensions. More can be read about this [here][6].

*Transparency:*

If a `transparentIndex` is defined for a frame, it means that any pixel within the pixel data that matches this index should not be drawn. When drawing the patch using canvas, this means setting the alpha value for this pixel to `0`;

### Drawing the GIF

Check out the **[demo][4]** for an example of how to draw/manipulate a gif using this library. We wanted the library to be drawing agnostic to allow users to do what they wish with the raw gif data, rather than impose a method that has to be altered.

### Thanks to

I underestimated the convolutedness of the GIF format, so this library couldn't have been made without the help of:

- [Project: What's In A GIF - Bit by Byte][7] - An amazingly detailed blog by Matthew Flickinger
- [jsgif][2]
- The LZW decompression from [this neat gist][8]

### Who are we?

[Unassigned][9]

[1]: http://ruffle.us
[2]: http://slbkbs.org/jsgif/
[3]: https://github.com/buzzfeed/libgif-js
[4]: http://matt-way.github.io/gifuct-js/
[5]: https://github.com/matt-way/jsBinarySchemaParser
[6]: http://www.matthewflickinger.com/lab/whatsinagif/animation_and_transparency.asp
[7]: http://www.matthewflickinger.com/lab/whatsinagif/index.html
[8]: https://gist.github.com/devunwired/4479231
[9]: http://unassigned.co