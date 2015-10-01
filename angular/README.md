# AngularJS Style Guide

*A mostly reasonable approach to AngularJS*

## Table of Contents

  1. [Basic Rules](#basic-rules)
  1. [Naming](#naming)
  1. [Declaration](#declaration)
  1. [Directives](#directives)
  1. [Controllers](#controllers)
  1. [Services](#services)

## Basic Rules

  - Your app should be thought of as a tree of components, not a large template with annotations. Follow component pattern principles.
  	Cleaner architecture, and is mandated by next gen frameworks (Angular2, React, etc)
  - Only include one angular component per file
  - Default to isolate scopes
  - All communication/state change should be explicit and "followable". Never modify an external scope without a very good reason. 
  - There should be a 1:1 mapping between controllers and templates. Don't use ngController or ngInit

## Naming

  - **Filename**: Use spinal-case for filenames. E.g., `example-service.js`.
  - **Reference Naming**: In general, use camelCase for angular components. PascalCase if it's a constructor or enum.
    ```javascript
    // bad
	module.service('SomeService');
	
	// bad
	module.service('some_service');
	
	// bad
	module.service('some-service');

    // good
	module.service('someService');
    ```

  - **Module Naming**: Module naming should be spinal-case and reflect module hierarchy
	
	```javascript
	// bad
	angular.module('someModule', [
		'someSubmodule',
	]);
	
	// good
	angular.module('some-module', [
		'some-module.services.submodule',
	]);
	```
	
  - **Component Naming**: Use the component name as the filename. For example, `module.service('exampleService')` should exist in a file named `example-service.js`.
    
	```javascript
    // bad
	// example.js containing
	module.service('exampleService');

    // good
	// example-service.js containing
	module.service('exampleService');
    ```


## Declaration

  - Component construction logic should be defined in an inline function expression, unless shared.

    ```javascript
    // bad
	.service('exampleService', exampleService);
	
	function exampleService() {
	}
	
	// good 
	.service('exampleService', function() { });
    ```
	
  - Only one angular component should exist per file, unless very tightly coupled.
	
	```javascript
    // bad
	.service('exampleService', function() { })
	.controller('exampleController', function() { });
	
	// good
	// In example-service.js
	.service('exampleService', function() {});
	
	// in example-controller.js
	.controller('exampleController', function() {});
		
	// good
	.directive('timeline', function() {})
	.controller('timelineController', function() {});
    ```

## Directives
	
  - Don't use ngController or ngInit directives. All template specific logic should be contained in a directive.
  - Don't use `controllerAs` syntax. Given a 1:1 mapping between controller and template, namespacing scope values is redundant.
  - Only use one of controller or link, unless absolutely necessary. For the majority of directives using one is sufficient.
  - Always default to isolate scope. Only in rare cases, such as performance sensitive ngRepeats, should other forms be used.
  
    ```javascript
    // bad
	.directive('timeline', function() {
		return {
			// Defaults to scope: false
		};
	});
	
	// bad
	.directive('timeline', function() {
		return {
			// Does not create new scope, uses existing scope in template
			scope: false, 
		};
	});
	
	// bad
	.directive('timeline', function() {
		return {
			// Creates a new child scope that prototypically inherits from containing scope.
			scope: true, 
		};
	}); 
	
	// good
	.directive('timeline', function() {
		return {
			// Creates an isolate scope. Only the properties specified in the scope object will exist on the initial scope.
			scope: {
				someProp: '=',
			}, 
		};
	}); 
	```
	
  - Declare controller in the same module as the directive, unless shared among multiple.
  
    ```javascript
    // good
	.directive('timeline', function() {
		return {
			controller: 'timelineController',
		};
	})
	.controller('timelineController', function() {
	});
	
	// If there are namespace collisions, an explicit linking/controller function can be supplied
	
	// good
	.directive('timeline', function() {
		return {
			controller: timelineController,
		};
		
		function timelineController() {
		}
	})
	```
	
  - Avoid use of `ngInclude`. `ngInclude` is an imperative construct that can almost always be replaced with a directive. It creates
	a maintainability issue by duping the filepath when used in more than one place, and the values it
	consumes are unclear.
	
	```html
	<!-- bad -->
	<ng-include src="some/long/path/who-knows/timeline.html"></ng-include>
	
	<!-- good -->
	<timeline></timeline>
	
	<!-- bad -->
	<ng-include src="{{ type === 'timeline' ? 'path/timeline.html' : 'path/waterfall.html' }}"></ng-include>
	
	<!-- good -->
	<div ng-switch="type">
		<timeline  ng-switch-when="timeline"></timeline>
		<waterfall ng-switch-when="waterfall"></waterfall>
	</div>
	
	<!-- ok, but can usually be reworked to avoid passing filepaths around in code -->
	<ng-include src="{{ completelyDynamicValue }}"></ng-include>
	```

## Controllers
	
  - Use `$scope` instead of assigning to `this` ala the `controller as` behavior. With a 1:1 mapping between controller and
	templates namespacing the scope values is redundant.

  - Watch out for inherited scopes "capturing" scope assignments.
    
	```html
	<!-- bad -->
	<div ng-repeat="item in items"> <!-- New inherited scope created by ngRepeat here -->
		<div ng-click="isAnyItemClicked = true"></div> <!-- Will be assigned to the inherited scope!! -->
	</div>
	
	<!-- good -->
	<div ng-repeat="item in items"> <!-- New inherited scope created by ngRepeat here -->
		<div ng-click="state.isAnyItemClicked = true"></div> <!-- Will be assigned to the expected scope -->
	</div
    ```
	
  - Use the controller mixin pattern when sharing common scope logic
  
	```javascript
	// bad
	.controller('controllerOne', function($scope, $controller) {
		$scope.controllerOneSpecificValue = 'hi';
		$scope.value = 5;
		$scope.$watch('value', doSomething);
		$scope.$watchCollection('[someValues, someOtherValues]', doSomeOtherStuff);
	})
	.controller('controllerTwo', function($scope, $controller) {
		$scope.controllerTwoSpecificValue = 'hello';
		$scope.value = 5;
		$scope.$watch('value', doSomething);
		$scope.$watchCollection('[someValues, someOtherValues]', doSomeOtherStuff);
	})
	
	// good
	.controller('controllerOne', function($scope, $controller) {
		$scope.controllerOneSpecificValue = 'hi';
		$controller('sharedLogicController', { $scope: $scope });
	})
	.controller('controllerTwo', function($scope, $controller) {
		$scope.controllerTwoSpecificValue = 'hello';
		$controller('sharedLogicController', { $scope: $scope });
	})
	.controller('sharedLogicController', function($scope) {
		$scope.value = 5;
		$scope.$watch('value', doSomething);
		$scope.$watchCollection('[someValues, someOtherValues]', doSomeOtherStuff);
	});
	```

## Services
  
  - Should be used to eliminate redundant code or share data between controllers

  - Public API should be as close to the top as possible.
  
  ```javascript
  .service('someService', function() {
	  
	  return {
		  exposedOne: exposedOne,
		  exposedTwo: exposedTwo,
	  };
	  
	  function exposedOne() {}
	  function exposedTwo() {}
  });
  ```

**[⬆ back to top](#table-of-contents)**