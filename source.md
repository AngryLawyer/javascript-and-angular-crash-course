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

# Dynamic, weak types
## a boon and a curse

Types in Javascript are dynamic - we don't care what goes into a slot.

    !javascript
    function logMe(lol) {
      console.log(lol);
    }

    logMe(7);
    logMe("a string");

    var x = 5;
    x = "five"

-----

This gives flexibility at the cost of having to make sure you don't call a function with silliness.

Types are also weak - the runtime will try and figure something out for a lot of cases. This is often mad.

    !javascript

    5 + 6; //11
    5 + '6'; //56

    false == 0 == undefined == null;

    false !== 0 !== undefined !== null;

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
    var square = function (x) {
      return x * x;
    };

    square(7); //49

    var map = function(callback, list) {
      var output = [];
      for (i in list) {
        output.push(callback(list[i]));
      }
      return output;
    };

    var result = map(square, [1,2,3,4]); //[1,4,9,16]
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

-----

#Angular time

-----

# Setting up a template

* Make sure you've got Node and NPM installed
* Make a new directory and cd into it
* npm install yo (might be npm install -g yo)
* npm install generator-angular
* yo angular testApp - follow the instructions, ignore the hipsterisms.
* npm install
* bower install
* grunt serve

-----

# Let's make an app!

Look at index.html

    !html
    <body ng-app="testApp">

We've attached testApp to the Body of the document. This is an example of using a directive.

Look at views/main.html - this gets injected into index.html. It's currently full of hipster guff. Clean it out - we'll start doing something interesting.

Let's just fill it with something simple.

    !html
    <blink>Hi!</blink>

And let's view it with grunt serve.
What a shame. Blink isn't supported in modern browsers. We need to bring back this important component of HTML!

-----
# Directives

Directives are things that represent either custom DOM elements, or custom attributes. We're going to use one to represent Blink tags.

Make a directory in scripts called 'directives'. Make a file called 'directives.js'

Edit index.html to have it.

-----

Open up directives.js - let's make our directive

    !javascript
    'use strict';

    var app = angular.module('testApp');

    app.directive('blink', [function () {
      return {
        restrict: 'E',
        transclude: true,
        template: '<span ng-transclude></span>',
        controller: 'BlinkController'
      };
    }]);

Here, we've defined that Blink is restricted to being an Element, Transcludes its content (so copies it inside its template), and uses BlinkController.

-----

Let's make it do something. Let's create a controller.

Edit controllers.js. Replace it with the following.

    !javascript
    'use strict';

    var app = angular.module('testApp');

    app.controller('MainCtrl', function ($scope) {
      $scope.awesomeThings = [
        'HTML5 Boilerplate',
        'AngularJS',
        'Karma'
      ];
    });

-----

Let's define our controller. Add the following

    !javascript
    app.controller('BlinkController', ['$scope', function($scope) {

    }]);

We now have a working directive, but it doesn't do much. Kinda justs sits there. We want it to blink. We can do this with data bindings.

-----

Open up our directive again. Let's add a visibility flag

    !javascript
    app.directive('blink', [function () {
      return {
        restrict: 'E',
        transclude: true,
        template: '<span ng-transclude ng-show="showing"></span>',
        controller: 'BlinkController',
        scope: {
        }
      };
    }]);

Here, we're using a built in 'show' directive, bound to a 'showing' variable. That's not going to work until we give it a value. We've also attached an empty scope to it. Scope is where functions for a controller and directive live. If you don't provide your own, it'll just use its parents.

-----

Open up our controller file again. Let's add the following:

    !javascript
    app.controller('BlinkController', ['$scope', function($scope) {
      $scope.showing = false;
    }]);

Go look at our app. IT'S GONE!

-----

Let's do the dirty work. Here, we change something on the scope every half second

    !javascript

    app.controller('BlinkController', ['$scope', '$timeout', 
      function($scope, $timeout) {
      $scope.showing = true;

      $scope.toggleTimeout = function () {
        $timeout(function () {
          $scope.showing = !$scope.showing;
          $scope.toggleTimeout();
        }, 500);
      };

      $scope.toggleTimeout();

    }]);

And as if by magic, the DOM is automagically updated after each timeout. But what if we want to have variable speeds?

-----

Add another blink tag to main.html

    !html
    <blink>hello world</blink>
    <blink speed="250">faster</blink>

-----

Now, let's pull that value through to our directive. Edit the directive.

    !javascript
    app.directive('blink', [function () {
      return {
        restrict: 'E',
        transclude: true,
        template: '<span ng-transclude ng-show="showing"></span>',
        controller: 'BlinkController',
        scope: {
          speed: '=speed'
        }
      };
    }]);

This means that, in $scope in the controller, $scope.speed is automatically set to whatever you've put in the binding. So, our speed is 250 for our second directive.

-----

In the controller, change it to this:

    !javascript

    app.controller('BlinkController', ['$scope', '$timeout',
      function($scope, $timeout) {
      $scope.showing = true;

      $scope.toggleTimeout = function () {
        $timeout(function () {
          $scope.showing = !$scope.showing;
          $scope.toggleTimeout();
        }, $scope.speed || 500);
      };

      $scope.toggleTimeout();

    }]);

We now, if we have a speed, use that, or fall back to 500.

But what if we want our default settable elsewhere? Let's make a Service.

-----

Add a new folder called services. Add a new file called services.js. Add it to index.html

    !javascript
    'use strict';

    var app = angular.module('testApp');

    app.service('BlinkSpeedManager', [function () {
      return {
        defaultSpeed: 1000
      };
    }]);

-----

Now, update our controller.

    !javascript
    app.controller('BlinkController', ['$scope', '$timeout', 'BlinkSpeedManager', function($scope, $timeout, BlinkSpeedManager) {
      $scope.showing = true;

      $scope.toggleTimeout = function () {
        $timeout(function () {
          $scope.showing = !$scope.showing;
          $scope.toggleTimeout();
        }, $scope.speed || BlinkSpeedManager.defaultSpeed);
      };

      $scope.toggleTimeout();

    }]);

Ta-da! We're now pulling it from a service, and if that service ever changes its value, it'll pull it through.

Let's make a new directive.

-----

    !html
    <blink>hello world</blink>
    <blink speed="50">faster</blink>
    <br/>
    <speed-munger></speed-munger>

And the directive

    !javascript
    app.directive('speedMunger', [function () {
      return {
        restrict: 'E',
        transclude: true,
        template: '<input ng-model="speed"></span>',
        controller: 'SpeedMungerController',
        scope: {
        }
      };
    }]);

-----

And the controller

    !javascript
    app.controller('SpeedMungerController', ['$scope','BlinkSpeedManager',
      function($scope, BlinkSpeedManager) {

      $scope.speed = BlinkSpeedManager.defaultSpeed;

      $scope.$watch('speed', function() {
        BlinkSpeedManager.defaultSpeed = $scope.speed;
      });
    }]);

OMG IT UPDATES EVERYTHING!
