Mixin Factory: a micro-framework for building mixin-based classes in JavaScript
===============================================================================

Dependencies: [Underscore](http://underscorejs.org)

Basic use:
---------
```
mixinFactory(foo = {}); //initialising the mechanism
                           
foo.factory("bar"); // creating our first factory 
                           
foo.bar("newClass",{ // defining a new class
   init: function(){ 
       // your codez ...
   },
   method_1: function(){
       // more codez ...
   } 
});

var newClassInstance = foo.bar.newClass(); // creates a new object of class ```newClass``` and factory ```bar```

```

Using mixins:
----------------

```
// Will create two new mixins for all classes of the bar factory
foo.barMixin("newMixin_1",{
    method_2: function(){
        // codez codez ...
    }
});

foo.barMixin("newMixin_2", {
    method_3: function(){
        // codez ...
    }
});
                           

// Now the mixins can be mixed into a class like this:

foo.bar("newClass", "newMixin_1", "newMixin_2",{
    // methods ...
});

// Or like that:
foo.bar("newClass", "newMixin_1 newMixin_2");

// Mixins can also be applied to a factory (this will automatically mix the mixin into all classes produced by bar):
foo.factory("bar", "newMixin_1");

// In fact a mixin can be a simple object reference:
var mixinObj = {
    //my codez
};

foo.bar("newClass", mixinObj, {
    // methods ...
});
```

Hooks
-----
Classes and mixins can define hook methods. These are different from ordinary methods in that:
* they must be prefixed with either "__before" or "__after"
* when two hooks with the same name are defined in two mixins, they would not try to override each other. Instead they are bundled and executed together when the hook is called.

Example:
```
foo.barMixin("newMixin_1",{
    __beforeInit: function(){
        console.log("beforeInit hook of newMixin_1");
    }
});

foo.barMixin("newMixin_2", {
    __beforeInit: function(){
        console.log("beforeInit hook of newMixin_2");
    }
});

foo.bar("newClass", "newMixin_1 newMixin_2",{
    init: function(){
        console.log("init of newClass");
    }
});
```
Now after running ```foo.bar.newClass()``` in your console you should see this:
```
beforeInit hook of newMixin_1
beforeInit hook of newMixin_2
init of newClass
```

```init``` and ```delete``` are the provided constructor and destructor methods. They call all attached "before" and "after" hooks automatically. If you wish to create a custom hook type your class will have to run it using a built-in runHook function:

```
foo.barMixin("newMixin_1",{
    __beforeSomeMethod: function(){
        console.log("beforeSomeMethod hook of newMixin_1", arguments);
    }
});

foo.barMixin("newMixin_2", {
    __beforeSomeMethod: function(){
        console.log("beforeCustom hook of newMixin_2", arguments);
    },
    __afterSomeMethod: function(){
        console.log("afterCustom hook of newMxin_2", arguments);
    }
});

foo.bar("newClass", "newMixin_1 newMixin_2",{
    someMethod: function(){
        this.runHook("beforeSomeMethod","some", "args", "here", 123, {bla: false});
        console.log("custom method of newClass");
        this.runHook("afterSomeMethod");
    }
});


var x = foo.bar.newClass();

x.someMethod();

```

The output will look like this:

```
beforeSomeMethod hook of newMixin_1 
["some", "args", "here", 123, Object]
beforeCustom hook of newMixin_2 
["some", "args", "here", 123, Object]
custom method of newClass
afterCustom hook of newMxin_2 []
```
                           
