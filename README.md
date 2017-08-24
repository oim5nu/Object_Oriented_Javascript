# Object_Oriented_Javascript
Experiments of Object Oriented Features In Javascript

### Prototype Chain
* functions have prototypes and objects have protos

#### Method 1

```
*// Consider constructor function as class in C++*
function DerivedClass() { 
  this.a = 1; 
  this.b = 2;
}

var sharedMember = { b:3, c:4 };
*// Consider prototype of constructor function as shared memebers, which are the same for each instance*
DerivedClass.prototype = sharedMember; 

var instance = new DerivedClass();
console.log(instance.b); *// 2, property shadowing*
console.log(instance.c); *// 4*
console.log(sharedMember.isPrototypeOf(instance)); *// true*
console.log(sharedMember.isProtoTypeOf(Obj)); *// thow Error*
```

#### Method 2 (rarely used)

```
var object0 = { b: 3, c: 4 }

var object = Object.create(object0, {a: {value: 1}, b: {value: 2}});

*// Prototype chain*
*// {a: 1, b: 2} ---> { b: 3, c: 4 } ---> null*
console.log(object.b); *// 2, property shadowing*
console.log(object.c); *// 4*
console.log(object0.isPrototypeOf(object)); *// true*
```

#### Method 3 (deprecated)
```
var object = {a: 1, b: 2};
var object0 = {b: 3, c: 4};

object.__proto__ = object0;

console.log(object.b); *// 2, property shadowing*
console.log(object.c); *// 4*
console.log(object0.isPrototypeOf(object)); *// true*
```
