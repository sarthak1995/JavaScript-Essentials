# THIS

'this' is a special keyword in Javascript that seems to be bit confusing or complex to understand, but with proper and clear understanding of the same, you will find it easy to deal with it in your code.

## Confusions

* 'this' refers to function itself

let us consider the following code

```
function func(){

	this.count++;

}

func.count = 0;

for(int i = 0;i<10;i++)
	func();

console.log(func.count) // 0
```

We know that functions in javascript are objects, but the common misconception here is that 'this' referes to function itself.

What you did, was created a global variable 'count' as NaN and incremented it.

The above problem can be solved by changind the code snippet as follows:

```
function func(){

	this.count++;

}

func.count = 0;

for(int i = 0;i<10;i++)
	func.call(func);

console.log(func.count) // 10
```


+ 'this' refers to function scope

Consider the following code snippet : 

``` 
function func1(){

	var val = 2;
	this.func2();

}

function func2(){
	console.log(func1.val); // undefined
}

```

It's often that programmers try to connect or make bridge between functions as described above. Its not possible to use 'this' as a reference to look something in the lexical scope


## What actually 'this' is?

When a function is invoked or called, an activation record is created which contains several information regarding where the function was invoked from (call stack), parameters passed to the function etc. One of the proerties of this record is 'this' reference which is used throughout the execution of the funciton.


'this' is therefore a _binding_ made when a function is invoked and it depends on **Call-Site** from where the function is called.


## Guidlines of Bindings

### Default Binding

Consider following scenario : 

```
function func(){
	console.log(this.count);   // <- 'this' refers to global object  
}

var count = 5;

func(); // 5

```

When we examine the call site of our function _func_, we see that it is invoked with plain, without any decorated function references. Hence, 'this' resloves to our global object.

### Implicit Binding

Binding is said to be impplicit if the call-site has an object context.

```
function func1(){
	console.log(this.val);
}

var obj = {
	val : 5,
	func1 : func1
}

obj.func1();

```

Since _func1_ is called from with the context of our object _obj_, implicit binding rule is applied.

But the above implicity mai be lost in some scenarios, and _default_  binding case may be applied for the same. For example, 

```
function func1(){
	console.log(this.val);
}

var obj = {
	val : 5,
	func1 : func1
}

var func2 = obj.func1; // <- creating a reference

func2(); // undefined

```

Also, our function may lose implicit binding in function callbacks.

```
function setTimeout(func,timeDelay){
	// wait for delayed time;

	func(); // <- call-site;
}


```

### Explicit Binding

Explicit binding is done if you want to force a function call to use a particular object for 'this' bindiing without putting a property refernce on the object as done in implicit binding.

We perform explicit binding with the help of utilities (call(...) and apply(...)) that are provided by every function via their _prototype_.

Both the utilites take first parameter as an object that should be use for 'this' and then invoke the function with that 'this' specifed, 

```
function func1(){
	console.log(this.val);
}

var obj = {
	val : 5,
}


func1.call(obj);

```

### Hard Binding

There are some cases in which explicit binding doesn't offer any help. Consider the following code : 


```
function func1(){
	console.log(this.val);
}

var obj1 = {
	val : 5,
}

var func2 = function(){
	func1.call(obj1),
}

obj2 = {
	val : 10;
}

func2() // 5

// func2's can not have its 'this' overridden
func2.call(obj2); // 5

```

Hence a reusable helper function can be created to solve the above scenario.
```
function func1(){
	console.log(this.val);
}

var obj1 = {
	val : 5,
}

var obj2 = {
	val : 10,
}

function bind(fn,obj){
	return function(){
		fn.apply(obj,arguments);
	}
}

var func2 = bind(func1,obj2);

func2();
```


We can also use the utiliy provided by JavaScript functions itself , called _bind(...)_


```
function func1(){
	console.log(this.val);
}

var obj1 = {
	val : 5,
}

var obj2 = {
	val : 10,
}

function bind(fn,obj){
	return function(){
		fn.apply(obj,arguments);
	}
}

var func2 = func1.bind(obj2);

func2();
```

_bind()_ returns a new function that calls the original function with 'this' context set by you.


### new Binding

Whenever a function is invoked with _new_ in front of it, A new object (constructor) is created, it is prototyped linked, it is set as 'this' binding for function call and it is returned unless some other object is returned by the function.

```
function Car(){
	this.wheels = 4;
}

var honda = new Car();
console.log(honda.wheels); // 4
```


## THIS and the Order Of Bindings

The above guidelines can be applied to the call site in the following order : 

1. If called with **new**, Use newly constructed object.
2. If called with call/apply/bind utility functions, use the specified object.
3. If called with a context object owning the call, use that object for context.
4. Else use Default or global object
