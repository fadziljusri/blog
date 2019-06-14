---
layout: post
title: JS Exif Image Orientation Displayed Wrongly, It's a BUG!!
permalink: /js-exif-image-orientation/
categories:
  - JavasSript
  - base64
  - Exif
  - JPEG
  - Orientation
---

The question is how to rotate the `base64` data image on the client side so that it displays correctly? (P.S. This usually happens with iPhone camera, android rarely)

### Problem...

![wrong_orientation](data:image/png;base64,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)

### Now its fixed 😊

![fixed_orientation](data:image/png;base64,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)

## Solution

For those who r lazy and just want to simple function to fix this bug, below are the code i found from stackoverflow [(https://stackoverflow.com/a/46814952)](https://stackoverflow.com/a/46814952) posted by [runxc1 Bret Ferrier](https://stackoverflow.com/users/30850/runxc1-bret-ferrier). Thanks dude!

```js
function getDataUrl(file, callback2) {
  var callback = function(srcOrientation) {
    var reader2 = new FileReader();
    reader2.onload = function(e) {
      var srcBase64 = e.target.result;
      var img = new Image();

      img.onload = function() {
        var width = img.width,
          height = img.height,
          canvas = document.createElement("canvas"),
          ctx = canvas.getContext("2d");

        // set proper canvas dimensions before transform & export
        if (4 < srcOrientation && srcOrientation < 9) {
          canvas.width = height;
          canvas.height = width;
        } else {
          canvas.width = width;
          canvas.height = height;
        }

        // transform context before drawing image
        switch (srcOrientation) {
          case 2:
            ctx.transform(-1, 0, 0, 1, width, 0);
            break;
          case 3:
            ctx.transform(-1, 0, 0, -1, width, height);
            break;
          case 4:
            ctx.transform(1, 0, 0, -1, 0, height);
            break;
          case 5:
            ctx.transform(0, 1, 1, 0, 0, 0);
            break;
          case 6:
            ctx.transform(0, 1, -1, 0, height, 0);
            break;
          case 7:
            ctx.transform(0, -1, -1, 0, height, width);
            break;
          case 8:
            ctx.transform(0, -1, 1, 0, 0, width);
            break;
          default:
            break;
        }

        // draw image
        ctx.drawImage(img, 0, 0);

        // export base64
        callback2(canvas.toDataURL());
      };

      img.src = srcBase64;
    };

    reader2.readAsDataURL(file);
  };

  var reader = new FileReader();
  reader.onload = function(e) {
    var view = new DataView(e.target.result);
    if (view.getUint16(0, false) != 0xffd8) return callback(-2);
    var length = view.byteLength,
      offset = 2;
    while (offset < length) {
      var marker = view.getUint16(offset, false);
      offset += 2;
      if (marker == 0xffe1) {
        if (view.getUint32((offset += 2), false) != 0x45786966)
          return callback(-1);
        var little = view.getUint16((offset += 6), false) == 0x4949;
        offset += view.getUint32(offset + 4, little);
        var tags = view.getUint16(offset, little);
        offset += 2;
        for (var i = 0; i < tags; i++)
          if (view.getUint16(offset + i * 12, little) == 0x0112)
            return callback(view.getUint16(offset + i * 12 + 8, little));
      } else if ((marker & 0xff00) != 0xff00) break;
      else offset += view.getUint16(offset, false);
    }
    return callback(-1);
  };
  reader.readAsArrayBuffer(file);
}
```

which can be easily called like below :
```js
getDataUrl(input.files[0], function(imgBase64) {
  // NOW the orientation is fixed at 'imgBase64'
  someElementImage.src = imgBase64;
});
```
