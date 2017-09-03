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

### II. Prototypal Pattern in Inheritance
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

### III. Module Pattern with Inheritance, Polymorphism
```
// Animal -- superclass
var AnimalModule = (function() {
  
  // constructor function
  var publicAPI = function(name, legs) {
    this.name = name || "Uknown";
    this.legs = legs;
  };
  
  publicAPI.prototype.getName = function() {
    return this.name;
  };
  
  publicAPI.prototype.talk = function() {
    console.log('Hi, ' + this.name + ' here.');
  };
  
  publicAPI.prototype.walk = function() {
    if (this.legs) {
      console.log('I could walk with my ' + this.legs + ' legs');
    } else {
      console.log('I could not walk, as I have no legs');
    }  
  }
  
  return publicAPI;
}());

// var beatle = new AnimalModule("Bettle", 4);
// beatle.talk();
// beatle.walk();
 
// Inheritance with polymorphism 
var MammalModule = (function(parentModule) {
  var __super__ = parentModule.prototype;
  var _hasHair = true;
  
  var publicAPI = function(name, legs) {
    parentModule.apply(this, arguments);
  };
  
  publicAPI.prototype = Object.create(__super__);
  
  publicAPI.prototype.talk = function() { // override talk methods
    __super__.talk.call(this);
    if (_hasHair) {
      console.log('I also have hair, as I am a mammal.');
    }
  }
  
  publicAPI.prototype.walk = function() {
    __super__.walk.call(this);
  }
  
  return publicAPI;
  
}(AnimalModule));


// var tiger = new MammalModule('Tiger', 4);
// tiger.talk();
// tiger.walk();

// Inheritance with methods extension
var fly = function(canFly) {
  if (canFly) {
    console.log("I could fly like birds!");
  } else {
    console.log("I could not fly.")
  }
};

var SpecialMammalModule = (function(parentModule, fly) {
  var __super__ = parentModule.prototype;
  var _canFly = true;
  
  var publicAPI = function(name, legs) {
    parentModule.apply(this, arguments);  
  };
  publicAPI.prototype = Object.create(__super__);
  
  publicAPI.prototype.fly = fly.bind(this, _canFly);
  
  return publicAPI;
}(MammalModule, fly));

var bat = new SpecialMammalModule('Bat', 4);
bat.talk();
bat.walk();
bat.fly();
```

### IV Inheritance with Module Pattern using interface enforcement

```
var Interface = function(name, methods) {
    this.name = name;
    this.methods = [];

    if (methods.constructor == Array)
        this.methods = methods;
    else if (methods.constructor == String)
        this.methods[0] = methods;
    else
        throw new Error("Interface must define methods as a String or an Array of Strings");
};

var InterfaceHelper  = {
    ensureImplements : function(obj, interfaces) {
       // If interfaces is not an array, assume it's a function pointer
       var toImplement = interfaces.constructor == Array ? interfaces : [interfaces];
       var iface;

       // For every interface that obj must implement:
       for (var i = 0, len = toImplement.length; i < len; i++) {
          iface = toImplement[i];

          // Make sure it indeed is an interface
          if (iface.constructor != Interface)
             throw new Error("Object trying to implement a non-interface. "
             + iface.name + " is not an Interface.");

          // Make sure obj has all of the methods described in the interface
          for (var j = 0, interfaceLen = iface.methods.length; j < interfaceLen; j++)
             if (!obj[iface.methods[j]])
                throw new Error("Interface method not implemented. " 
                + iface.name + " defines method " + iface.methods[j]);
       }

       return true;
    }
};

var AnimalModule = (function() {
  
  var publicAPI = function(mouth) {
    this.mouth = mouth;
  };
  
  publicAPI.prototype = {
    constructor: publicAPI,
    speak: function() {
      if (this.mouth) {
        console.log('I have mouth, and I could speak.');
      } else {
        console.log('No mouth no speak.')
      }
    },
    run: function() {
      console.log('Yes, I could run!');
    }
  }
  return publicAPI;
}()); 

var tiger = new AnimalModule("Big Mouth");


var AnimalModule = new Interface("AnimalModule", ["speak", "run", "eat"]);

// may raise error, since eat has not been implemented
function displayRoute(animal) {
  InterfaceHelper.ensureImplements(animal, AnimalModule); 
  animal.speak(); 
  animal.run();
}

displayRoute(tiger);
```

Reference: 
https://www.gellock.com/2016/01/05/javascript-inheritance-patterns/
http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html
"Pro Javascript Design Pattern" byRoss Harmes and Dustin Diaz, p47
