## THIS

'this' is a special keyword in Javascript that seems to be bit confusing or complex to understand, but with proper and clear understanding of the same, you will find it easy to deal with it in your code.

# Confusions

1. 'this' refers to function itslef

let us consider the following code

```
function func(){

	this.count++;

}

func.count = 0;

for(int i = 0;i<10;i++)
	func();

console.log(func.count) // Prints '0'
```

We know that functions in javascript are objects, but common misconception is that 'this' referes to function itself.