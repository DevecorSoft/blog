# Test vue components from consumer perspective

To be from consumer perspective, what should we test for a vue component?

we test vue compoent from consumer perspective aiming to write the simplest code that passes the new test.


## data timeline(unit test)

To focus on data, instead of view, since vue.js provides efficient reactivity that is core feature of this framework. In other word, do not test vue functionality!

Mounting method: `shallowMount`

Example:

when xxx component mounted
  then a property that bind to style is initiated with empty object
  then a property that indicate url to image is empty string

when user try to paste an image from clipboard into image container
  then base64 string should be set into the style-binding property as background url
  then should asign the image url property with url that is got from upimage server
  then the url to image shoould be copied into clipboard

when user click the copy button
  then then url to image should be copied into clipboard

## view timeline(component test)

To focus on some important elements that are responsible for business, instead of concrete element like img, input, etc.

Example:

Mounting method: `mount`

when xxx component mounted
  then an empty image container should exists
  then an empty text view indicate url to image should exists
  then a button that I can use it to copy url

when user try to paste an image from clipboard into image container
  then the image should be shown on this container
  then a url to this image should be filled in text view
  then the url to image should be copied into clipboard

when user click the copy button
  then the url to image should be copied into clipboard
