# vue组件测试范式与风格

从消费者视角看看vue组件应该测什么？

从消费者视角来实践TDD能相对容易达到“实现刚刚好通过测试”目的

因为vue组件是ui组件，为ui组件编写测试时不得不根据vue组件的生命周期，在合适的时间节点测试我们期望的数据状态或者ui交互
我们想要的组件未必能给。理解组件的基本工作方式是必要的，在编写组件测试之前，不得不了解vue[组件生命周期](https://vuejs.org/guide/essentials/lifecycle.html)。组件完全发挥作用的时间节点是挂载之后（mounted）。

## data时间线(unit test)

关注的是数据而不是视图

组件挂载方式：`shallowMount`

when xxx component mounted
  then a property that bind to style is initiated with empty object
  then a property that indicate url to image is empty string

when user try to paste an image from clipboard into image container
  then base64 string should be set into the style-binding property as background url
  then should asign the image url property with url that is got from upimage server
  then the url to image shoould be copied into clipboard

when user click the copy button
  then then url to image should be copied into clipboard

## view时间线(component test)

关心ui层的某些重要的，承载业务职责的元素，而不是具体的img或者input元素。例如

组件挂载方式：`mount`

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
