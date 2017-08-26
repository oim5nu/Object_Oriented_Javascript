# Object_Oriented_Javascript
Experiments of Object Oriented Features In Javascript

### I. Prototype Chain
* A prototype chain is a linked-list of objects pointing backwards to the object from which each one inherits.
* functions have prototypes and objects have protos

#### Method 1

```
// Consider constructor function as class in C++
function DerivedClass() { 
  this.a = 1; 
  this.b = 2;
}

var sharedMember = { b:3, c:4 };
// Consider prototype of constructor function as shared memebers, which are the same for each instance
DerivedClass.prototype = sharedMember; 

var instance = new DerivedClass();
console.log(instance.b); // 2, property shadowing
console.log(instance.c); // 4
console.log(sharedMember.isPrototypeOf(instance)); // true
console.log(sharedMember.isProtoTypeOf(Obj)); // thow Error
```

#### Method 2 (rarely used)

```
var object0 = { b: 3, c: 4 }

var object = Object.create(object0, {a: {value: 1}, b: {value: 2}});

// Prototype chain
// {a: 1, b: 2} ---> { b: 3, c: 4 } ---> null
console.log(object.b); // 2, property shadowing
console.log(object.c); // 4
console.log(object0.isPrototypeOf(object)); // true
```

#### Method 3 (deprecated)
```
var object = {a: 1, b: 2};
var object0 = {b: 3, c: 4};

object.__proto__ = object0;

console.log(object.b); // 2, property shadowing
console.log(object.c); // 4
console.log(object0.isPrototypeOf(object)); // true
```
### II. Classical Inheritance
#### Single Inheritance
```
// Animal -- superclass
function Animal(name) {
  this.name = name;
  this.size = '10';
}

// superClass method
Animal.prototype.saySomething = function() {
  console.log('I am ' + this.name);
}

function Mammal(name, hair) {
  Animal.call(this, name); // call superclass constructor
  this.hair = hair;
}

// subclass extends superclass
Mammal.prototype = Object.create(Animal.prototype);
// Mammal.prototype = new Animal(); // may need arguements to have instances, less efficient
// Mammal.prototype.constructor = Mammal;

var mammal = new Mammal("Mammal Big", 'brown hair');

mammal.saySomething();
console.log('mammal name: ', mammal.name); // Mammal Big
console.log('mammal size: ', mammal.size); // 10
console.log('mammal hair: ', mammal.hair); // brown hair
console.log(mammal instanceof Animal); // true
console.log(mammal instanceof Object); // true
console.log(mammal.hasOwnProperty('saySomething')); // false, inherited
console.log(mammal.hasOwnProperty('name')); // true
console.log(Animal.isPrototypeOf(Mammal)); // false
```

#### Multiple Inheritance
```
// Animal -- superclass
function Animal(name) {
  this.name = name;
  this.size = '10';
}

// superClass method
Animal.prototype.saySomething = function() {
  console.log('I am ' + this.name);
}

// subclass
function Mammal(name, hair) {
  Animal.call(this, name); // call superclass constructor
  this.hair = hair;
}

// subclass extends superclass
Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;

// Plant -- another supperclass
function Plant(leaves) {
  this.leaves = leaves;
}

Plant.prototype.growInSoil = function() {
  console.log('I may grow in Soil.');
}

// Multiple inheritance 
function Monster(name, leaves) {
  Animal.call(this, name);
  Plant.call(this, leaves);
}

Monster.prototype = Object.create(Animal.prototype);
// Mixins
Object.assign(Monster.prototype, Plant.prototype);
Monster.prototype.constructor = Monster;

var monster = new Monster('Handsome Monster', 'Green Leaves');
monster.saySomething();
monster.growInSoil();

console.log(monster instanceof Animal );
console.log(monster instanceof Monster );
console.log(monster instanceof Plant );
```
