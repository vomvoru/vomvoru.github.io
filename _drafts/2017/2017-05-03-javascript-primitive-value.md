```js
var color1 = new String('red');
var color2 = new String('red');
var color3 = 'red';
var color4 = 'red';

console.log(color1 == color2)
console.log(color2 == color3)
console.log(color3 == color4)
console.log('--------------')
console.log(color1 === color2)
console.log(color2 === color3)
console.log(color3 === color4)
console.log('--------------')
console.log(color1.valueOf() === color2.valueOf())
console.log(color2.valueOf() === color3)
console.log('--------------')
console.log(color1.constructor === color2.constructor)
console.log(color2.constructor === color3.constructor)
console.log('--------------')
console.log(color1 instanceof String)
console.log(color3 instanceof String)
console.log(color1.valueOf() instanceof String)
console.log(color3 instanceof Object)
console.log('--------------')
console.log(typeof color1)
console.log(typeof color3)
```
