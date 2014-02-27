# Javscript and AngularJS
## Or: How to work with Tony without making him pull the rageface

-----

# A quick run through of Javascript

    !javascript

    var count = 0;

    function sayHello(name) {
        count += 1;
        console.log("Hello, " + name + " - this is the "
          + count + " time you made me say hi.");
    }
    sayHello("llamas");

* Dynamically typed language (like Python)
* C-like syntax (like Java)
* Combination of cleverness and WTF

-----

# Dynamic types - a boon and a curse

    !javascript

    5 + 6; //11
    5 + '6'; //56

-----

# Pretty simple object system
## if you don't look too deeply

    !javascript
    var x = {}; //An empty object or dictionary

    var point = {x: 5, y: 20}; //A dictionary representing a point

    var myObject = {
      setMonchness: function (monchness) {
        this.monchness = monchness;
      }
    }; //An object

    myObject.setMonchness(9001); //OVER NINE THOUSAND

-----
    
# First class functions
## perhaps the most useful thing you'll ever see

    !javascript
    //We can assign them to variables
    var double = function (x) {
      return x * x;
    };

    double(7); //14

    var map = function(function, list) {
      var output = [];
      for (i in list) {
        output.push(function(list[i]));
      }
      return output;
    };

    var result = map(double, [1,2,3,4]); //[2,4,6,8]
    var result = map(function(x) { return x + 1;}, [1,2,3,4]); //[2,3,4,5]

-----

# Closures - pretty bonkers

    !javascript
    var partialAdd = function(first) {
      return function(second) {
        return first + second;
      };
    };

    console.log(first); //Undefined
    var addToSeven = partialAdd(7);
    console.log(first); //Undefined
    console.log(addToSeven(13)); //20 - magic

* The function returned from partialAdd has 'closed over' the variable first, taking it with it, wherever it goes.
