# Rust Is Simple

It is my revision of popular book "The Rust Programming Language" by Steve And Carol.

### Why Rust?
- It is fast 
- It is type safe
- It is scalable
- It has package site called crates.io

## Installation
```
$ curl https://sh.rustup.rs -sSf | sh
```

### Updating
```
$ rustup update
```
### Finding Version
```
$ rustup --version
```

### Uninstalling
```
$ No I don't want to uninstall it
```
### Hello world program
Create a filename `main.rs` and type the following code.
```
fn main() {
	println!("Hello, World!");
}
```

To compile:
```
$ rustc main.rs
```

It should generate executable named main so you can run program like this

```
$ ./main
```

### Use Cargo
It's a awesome package manager and it makes task easier.

### Checking version of cargo
As a expect
```
$ cargo --version
```

### Scaffolding a project
```
cargo new hello --bin
```

If you don't specify `--bin` it means it is library intended to be distributed. Probably you will distribute on cargo. 

Cargo uses TOML for configuration.

### Hello world using cargo
- Scaffold project using cargo
- Type the hello world progam on `src/main.rs` file then directly compile using following command
```
$ cargo run
```

# Guessing game
Please don't be afraid just read a code and try to analyse what this do. Everything will be explained on upcoming topics.

Scaffold using cargo

```
$ cargo new guessing_game --bin
$ cd guessing_game
```

Check if everything looks correct in `Cargo.toml` file

In `src/main.rs` file type the following boilerplate code which just take user input

```rs
use std::io;
fn main() {
	println!("Guess the number!")
	println!("Please input the guess")
	let mut guess = String::new();

	io::stdin().readline(&mut guess)
		.expect("Failed to read line");

	println!("You guess: {}", guess);
}
```

Ok lets not be scared. 
First every rust program start with `main()` function. 

`println!` is a macro which print its first parameter at console. `{}` inside the println! first parameter is placeholder which is replaced by second, third ... parameter. If there is only one `{}` placeholder it is replaced by second. If there is two then it is replaced by 2nd and 3rd parameter. 

We need to get input from user so we will use standard library which you we can import using `std::io`  library . IO means input output. 

To create a variable we use `let`. In rust variables are immutable by default thats why we need to use `let mut` if you want to change it. So we defined `guess` as mutable variable that will be instance of String. 

Inside io library we use `stdin().read_line` method and pass the parameter which will get inputed. So when user input that data will get stored to `guess` variable. As readline method need to change the variable we pass guess by reference so we use `&mut guess`. 

The readline method returns us io::Result. Results type are enumerations. Results can be of two variant named `Ok` and `Err`. We we get `Ok` variant we means we got things correctly. If there is `Err` this means there is error on operation and we will need to handle it. To handle it we will use `expect` method. We can use expect method because the `Result` provided by io library has got expect method.

We need to use expect because if we think about it user can input nothing and we will need to handle it.

#### Use crate to grab random number generator library
We are going to use a library name rand which can be used to generate random numbers.
For that go and add the following line below `[dependencied]` at `Cargo.toml` file
```
rand = "0.3.14"
```

lets use the random number generator library
Modify the boilerplate code to look like this:
```
extern crate rand;

use std::io;
use rand::Rng;

fn main()  {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	// We print just to know what is secret number
	println!("The secret number is {}", secret_numner)

	let mut guess = String::new();

	io::stdin().read_line(&mut guess).expect("Failed to read line");

	println!("You guessed: {}", guess);
}
```
ok lets talk about rand library.
First we need to let rust know there is an external dependencies thats why you use `extern crate rand`
After that we use Rng `trait` provided by rand library. And you know there are varios way to generate random number. Like using external entropy, time etc. We are going to use `thread_rng` generator. (I don't know exact implementation). After knowing which generator to use we call gen_range function which takes minimum number inclusive and maximum number exclusive.

If you want to look the documentation of rand library we you can use this command.

```
	cargo doc --open
```

It will show docs relaated to `rand` because you imported it.

Now we need to compare the `secret_number` and `user_input`
Change main.rs code to following

```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	// We print just to know what is secret number
	println!("The secret number is {}", secret_numner)

	let mut guess = String::new();

	io::stdin().read_line(&mut guess).expect("Failed to read line");

	println!("You guessed: {}", guess);

	match guess.cmp(&secret_number) {
		Ordering::Less => println!("Small"),
		Ordering::Greator => println!("Big"),
		Ordering::Equal => println!("You win"),
	}
}
```
Ordering is a enum we imported which has variant called `Less`  `Greator` , `Equal`
`cmp` method compares two values and returns any one variant of `Ordering`
In rust we can use `match` expressing which is made up of arms.

If you compile this you will get error because you cannot compare apple with banana.

Secret number is integer and user input is string. You may have wondered already why we are not using integer from userinput.

Ok lets parse the user input to integer so we can compare integer with integer.

```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	// We print just to know what is secret number
	println!("The secret number is {}", secret_numner)

	let mut guess = String::new();

	io::stdin().read_line(&mut guess).expect("Failed to read line");

	let guess: u32 = guess.trim().parse()
								.expect("Please give a number");

	println!("You guessed: {}", guess);

	match guess.cmp(&secret_number) {
		Ordering::Less => println!("Small"),
		Ordering::Greator => println!("Big"),
		Ordering::Equal => println!("You win"),
	}
}
```

If you see we added new `let guess` but `guess` was already defined. Rust allows it and it is called shadowing . We shadowed guess :P. 

So now we know type of new guess should be interger thats why we used `:u32`. 

After that we trimmed because we don't want line feed to appear on guess variable and we parsed it.  As we specified type of guess should be u32 while parsing rust knows it should be `u32`.

There can be error thats why we used `.expect` method . Image user didn't gave any input and entered it. And we can use expect method because parse() retuns `Result` which  is enum and has `Err` and `Ok` variant.

ok now program should compile easily :).

Now lets allow multiple guess with loopppppping
in rust we can use `loop{}` for looping.

```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	// We print just to know what is secret number
	println!("The secret number is {}", secret_numner)
	loop{
		let mut guess = String::new();

		io::stdin().read_line(&mut guess).expect("Failed to read line");

		let guess: u32 = guess.trim().parse()
									.expect("Please give a number");

		println!("You guessed: {}", guess);

		match guess.cmp(&secret_number) {
			Ordering::Less => println!("Small"),
			Ordering::Greator => println!("Big"),
			Ordering::Equal => println!("You win"),
		}
	}
}
```

Now the program will run forever. Lets add a feature that program will stop running after correct guess
```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	// We print just to know what is secret number
	println!("The secret number is {}", secret_numner)
	loop{
		let mut guess = String::new();

		io::stdin().read_line(&mut guess).expect("Failed to read line");

		let guess: u32 = guess.trim().parse()
									.expect("Please give a number");

		println!("You guessed: {}", guess);

		match guess.cmp(&secret_number) {
			Ordering::Less => println!("Small"),
			Ordering::Greator => println!("Big"),
			Ordering::Equal => {
				println!("You win");
				break;
			} 
		}
	}
}
```
You can see we modified `Ordering::Equal` arm to break loop when user win.

Lets handle invalid input also. Lets continue to break the loop when user gives invalid input instead of crashing the program

```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	// We print just to know what is secret number
	println!("The secret number is {}", secret_numner)
	loop{
		let mut guess = String::new();

		io::stdin().read_line(&mut guess).expect("Failed to read line");

		let guess: u32 = guess.trim().parse() {
			Ok(num) => num,
			Err(_) => continue,
		}

		println!("You guessed: {}", guess);

		match guess.cmp(&secret_number) {
			Ordering::Less => println!("Small"),
			Ordering::Greator => println!("Big"),
			Ordering::Equal => {
				println!("You win");
				break;
			} 
		}
	}
}
```

You can see we modified .expect to 
```
{
	Ok(num) => num,
	Err(_) => continue
}
```

It is because parse method gives us `Result` enum which has `Ok` and `Err`. When there is `Ok` it mean use has given valid input. And when there is error it will go to Err arm. And we want to catch all type of error so we use undercore `_`.

Here is final program.
```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
	println!("Guess the number");
	let secret_number = rand::thread_rng().gen_range(1,101);
	loop{
		let mut guess = String::new();

		io::stdin().read_line(&mut guess).expect("Failed to read line");

		let guess: u32 = guess.trim().parse() {
			Ok(num) => num,
			Err(_) => continue,
		}

		println!("You guessed: {}", guess);

		match guess.cmp(&secret_number) {
			Ordering::Less => println!("Small"),
			Ordering::Greator => println!("Big"),
			Ordering::Equal => {
				println!("You win");
				break;
			} 
		}
	}
}
```
We just removed printing secret number so user don't know answer from beginig.
Compile it to test if it works properly or not.
```
$ cargo run
```

Congratulation you build your first game :)

# Common Programming Concept

Keywords : Rust has reserved some words which we cannot use for naming variables and functions.

Variables are immutable by default but you want to mutate it use `mut`

For large data mutating may be faster than copying allocated instances . With samller creating new instances and writing in more functional programming is usally benificial.

Immutable variable and constant differes in way that const must have type annotated and can only be constant expression not result of function call etc.
Example of constant

```
const MAX_POINT: u32 = 100_000;
```

As a convention we use full upcase and for integer we use _ to represent comma.

Example of shadowing
```
let spaces = "   ",
let spaces = spaces.len();
```

Shadowing is benificial because we don't need to create new variable .

### Data Type
Rust is static type language. It has scalar type and 

Rust has four scalars: Integer, floating-point numbers, booleans, and characters.

Integer type in rust is represened to i8,i16,i32,i64, isize. Unsigned Integer is represented by u8, u16, u32, u64, usize.

isize means if you are on 64 bit computer it is i64. 

Rust use i32 for default and f64 for floating point number.

Floating point number can be used by f32 and f64 . f64 is default

```
let x = 2.0; // f64
```

Rust supports mathematical operation like addition +, subtraction  -, product * , quotient /, remainder %

Boolean are simply `true` and `false`
```
let tr = true; // Implicit type annotation
let fa: bool = false; // explicit type annotation
```

Charecter type is primitive alphabet and represents unicode scalar value. It supports not only ASCII but japanese, emoji etc.

### Compound type
Rust has two primitive compound type called `tuples` and `arrays`

For defining tuples use paranthesis `()`
```
let less_than_seven = (1,2,3,4,5,6,7) // Implicit type annotation
let num: (i32,f64,i32) = (1, 2.3, 4) // Explicit type annotation
```
Destructing tuple is simple just use paranthesis like this
```
let num = (1, 2.3, 4);
let (x,y,z) = num;
```
You can also access tuple by dot syntax `.`.
```
let num = (1, 2.3, 4);
println!("{} {}", num.0, num.1);
```


Arrays are collection of multiple values with same type. Tuple can be of different type but arrays are same type and have fix length. Arrays are stored in stack. 

You can access array using big bracket `[]`
```
let day = [1,2,3,4,5];

let third = day[2]
```

Arrays are zero indexed.

#### Rust Functions
snake case is convention for naming functions. function is defined by using `fn`

Example
```
fn summer (x: i32, y: i32) {
	let sum = x + y;
	println!("Sum: {}", sum);
}
```

So function can take argument and return a value . In Rust you need to declare all type of argument.

Function can return expressions. Expressins are something that gets evaluated to resulting values.

Example of statement
let x = 1;

In rust you cannot assign statement with variable

so `let y = let x = 1` is invalid 

`1 + 1` is expression so we can assign it to variable

Function that returns expression can be assigned to variable.
```
let z = 3;
let x = {
	let z = 1;
	z + 1
}
```

here x will be 4 . Because `{}` is an expression which is use to create scopes. Remember we didn't used semicolon at end of scope.

In rust you can define return type using  arrow `->`

```
function summer(x: i32, y: i32) -> i32 {
	x + y
}
```

remember we didn't semicoloned x + y so that it act as expression. If you semicolned it will implicitly return () and it should throw error because we have said by using arrow we expect return type to be `i32`. It is beauty of types that you can catch such error easily.

In rust you can use `//` to comment. Remember our code should act as documentation so please lets not missue the comment feature. Its for edges cases where we want another developer to understand what we intend :)

### Control flow
We have `if` `else if` `else` `for` `while` etc for controlling the flow

`if ,else if, else` can be easily explained by example

```
let x = 3

if x == 1 {
	println!("Its one");
} else if x == 2 {
	println!("Its two");
} else {
	println!("its three");
}
```

Rust need boolean in condition part of `if` , `else if` statement. Its is not like otherlanguage which coerce to boolean.


You can assign variable with if but all condition should return same type and should be expression.

Looping is easy in rust . You can use loop{} , `while` or `for`

```
let mut number = 10;
while number != 0 {
	println!("{}", number);
	number = number - 1;
}
```

Looping with for

```
let a = [1,2,3,4,5]

for element in a.iter() {
	println!("{}", element);
}
```

For looping coundown prefer for loop because of safety and consiness of for loop

```
for number in (1..4).rev() {
	println!("{}", number);
}
```

`(1..4)` means range which is from `1,3` so you should guess `4` is excluded .

```
// Todo Exercises
```

## Ownership
Rust don't have garbage collector like that of Java. It has a robust feature called ownership. It is simple to understand but it implications are too much powerful.

Stack is data structure which is last in first out. Think like stack of books, plates which you can take out from last only. You cannot take from middle. So you can add item on last  and pull out from last. So Push and Pull operation are done on stack.

Heap is poooooooool of memmory. So you can fit unknown size input and increase or decrese according to our desire.

### Ownership Rules
1. Each value in rust has a variable that called its owner
2. There can only be one owner at a time ! Solo owner
3. When owner goes out of scope it will be dropped. So owner is _rusted_ (killed) if we go out of scope :P


In rust scope start with opening curly brace `{` and end it closing curly brace `}`
``
{  // x is not yet defined
 	let x = 3 // x is valid from now
 	// x is available
} // scope gone x gone :_
``

All primitive which we used till  like int, boolean etc are created on stack and when scope is over they are poped off. So now the one type that works on heaps is `String`. So you might have guess `String` is not easy. 

You must understand that string literal and `String` is different. String literal are immutable. And every string is not determined at time of writing code so we need our second string type provided by Rust called `String`.

```
let mut x = String::from("hello"); // create a string hello
s.push_str(", world!") // Now our string is hello, world
```

So we should know by now string literals are found on final executables that we compile. so its immutable.  But imagine you need to create string from user input. Here we cannot use literal and we are forced to use our handy dandy `String`

Now we used heaps and we don't have garbage collector how are we supposed to clean memory?. And sometime while cleaning we may clean twice causing crashes. We need one allocation on heap and one free. Rust is different. It deallocates memory when variable when goes out of scopes it returns back the memory. 

When variable goes out of scopes Rust call drop. So when closing paranthseis is reached drop is called automatically and memory is cleaned up.

Now its time to examine code

```
let x = 1;
let y = x;
```

Here 1 is integer so when let y =x is done copy of x is binded to y.

But you can think `String` made on heap is too large. So copying and binding is inefficient. So what happen if we do like this

```
let x = String::from("hello world i am large text");
let y = x;
```

You know x is large text so when we call let y = x if we again make copy in heap it is expensive and We have already said rust is fast. If we waste memory like this how will it be this fast .
So what happens is when we create `String` it returns stack of memory which has  pointer where it is residing, length and capaciy. Length means how much memory string is using. Capacity means total amount of memory operating system has given to string.

When we assign let y = x; We copy stack related to x not whole heap .  

Now lets get moving when rust go to out of scope drop function is called to clean up heap memory. And `x` and `y` are on same scope so when they drop do memory get double freed?

Double free is problematic so rust has plan to handle it.

When we assign let y = x; Rust will remove x1 ownership of heap and make y as owner of that heap (by heap mean i mean memory where our large string is living).

And as x is removed the problem of double free never arises.

We can call it we moved x to y when we do
```
let x = String........
let y = x
```

But what should we do if we want to make a copy? We can use clone

```
let s1 = String::from("hello");
let s2 = s1.clone
```
Now both s1 and s2 are poiting to different location of heap.

I hope we understood how heap and stack copy works :) Assiging variable means copying stack.

Copying stack doesn't create problem on primitive type like integer because they uses special annotation called Copy trait. If type has copy trait it is reusable after assignment. Rust don't allow to implement copy trait if type has implemented Drop which our `String` has.


### Ownership and function

When we pass value to function it is like assining value to a variable. Passing a variable to function will have similar implication of moving or copying.

```
fn main() { // lets call this scope a
	let x = String::from("hello"); // x is in scope a
	takes_ownership(x); // Now x is no longer valid because it is moved to this function 
	let y = 5;
	makes_copy(y); // Y will move to its function but is is integer it has copy trait implemented means y will still be 5.
}

fn take_ownership(some_string: String) { // some_string come in new scope
	... // do something you love
} // somestring goes out of scope so is dropped by rust and memory is cleaned up.

function makes_copy(some_integer: i32) { //some_integer comes to scope
	// do something with this some_integer
}	// Here some integer goes out of scope and stack is poped of like usual
```

### Return value and scope
```
fn main() {
	let s1 = gives_onwership; // s1 will be ownership of something. See this function signature.

	let s2 = String::from("hello"); // s2 comes to scope
	let s3 = takes_and_gives_back(s2); // s2 is moved to parameter. But this function return value which again rebined to s3.
}// s3 goes out of scope so rust drop it and memory is cleaned. s2 is moved so nothing needs to be done. s1 goes out of scope so is dropped by rust.

fn gives_ownership() -> String {
	let something = String::from("something");

	something // it is return and it is donated/returned to calling function. Enjoy something caller you are lucky
}

fn takes_and_gives_back(a_string: String) -> String {
	a_string
} // ntohing happens as a_string is returned so is moved to calling function
```


So to get our ownership we need to return back which is too much pain isn't it? So to reduce our pain here comes a hero: References.

We needed to return back ownership just because  we still want to bind that variable to same location on heap. Instead of returning ownership and too much painful job we can easily to references

```
fn main() {
	let something = String::from("Something");
	let len = calculate_length(&something);

	// What happens to something . As we passed something by references something is still owner of that heap. So something is still the string here.
}

fn calculate_length(s: &String) -> usize { // usize means u32 or u64 os you are using.
	s.len()
}
```
As we used reference something is not gone away. References allow to refer value without taking ownership of it. And in calculate function at end of scope s don't have any ownership nothing happens.

In rust call having references parameters mean borrowing. We borrow it and when we have done we need to give it back to original thing that gave us.

We can have mutable borrowing but we can have only one mutable borrowing in a scope.

let following causes exception

```
let mut s = String::from("hello");

let s1 = &mut s;
let s2 = &mut s;
```
It throws error because rust wants to protect us from data race. And data race is too much problematic on real life.

We cannot even have immutable and mutable reference at same time . This is to protect from dangling references.
so following code throws error
```
let s = String::from("hey jude");
let x = &s; // no problem
let y = &s; // no problem
let z = &mut s; // Big problem
```

It is because let say z was freed. It causes x and y to refernces to memory that was freed. And that is big problem.  You may have got garbage on C and C++ when you reference memory that no longer exist .


Slices
Slides don't have ownership. Slices let you references a sequancial part of collection rather than whole collection. 

So lets program to get slices of string. First we will find index of slice of string where first word end. So basically we are finding index of end of first word.

```
fn main() {
	let s = String::from("hello world"); // Our objective is to get index of o.
	let word = first_word(&s); // we don't want ownership and don't want s to be mutated too.
}

fn first_word (s: &String) -> usize {
	let bytes = s.as_bytes();

	for (i, &item) in bytes.iter().enumerate() {
		if item == b' ' {
			return i;
		}
	}
}
```
So we created a string on heap. Passed it by reference to word.

At first word we are expecting reference of string as parameter and we return integer.

we convert string to bytes so that we can find space. We convert string to bytes by using `as_bytes` method on `String`. It returns array of bytes. We makeit iterable and enumerable which returns tuple of index and reference to value of iterable . Then we compare if item was binary space . If it was we return index.

It is fairly simple right.

But it has a drawback.

Like for this example

```
fn main() {
	let mut s = String::from("hello world");
	let word = first_word(&s);

	s.clear();
}
```

here we called s.clear(); but if we investigate value of word it is 5. Because even after clearing s the word doesn't have any impact as it is just unsigned integer. So we will run into trouble if we use that `word` later thinking s is old value.

So this can cause problem unknowinlgy . Rust have a effective solution by using string slices.

String slide is reference to part of string

```
let s = String::from("hello world");
let hello = &s[0..5]; //hello
let world = &s[6..11]; //world
```

In rust following are equivalent
```
let s = String::from("hello");
let slice = &s[0..2]
let slide = &s[..2]
```

following are also equivalent
```
let s = String::from("hello");
let len = s.len();

let slice= &s[3..len];
let slice = &s[3..];
```


so lets write the first word function that returns a slice instead of index.

```
fn first_word(s: &String) -> &str {
	let bytes = s.as_bytes();

	for (i,&item) in bytes.iter().enumerate() {
		if item == b' ' {
			return &s[..i];
		}
	}
}
```

now with this error are hard to occur.

Hmm now we have worked with slices we need to know primitive string are just a string slices. So they are immutable

```
let x = String::from("hello world");
let y = x[..]; // it is string slice and there is no mut so it is immutable
```
String slices are defined on similar way thats why they are immutable.
Knowing this has a implication how we pass parameter to function

So we currently have function signature to look like this
```
fn first_word(s: &String) -> &str {}
```
we can refactor it to
```
fn first_word(s: &str) -> &str {}
```

Now we can pass string literals because they are slices too. And we can pass `&String` slices too by `&String[..]`
Other slice
There is array slice too
```
let a = [1,2,3,4,5];
let slice = &a[1..3] 
```

Ok I hope we got meaning of ownership, memory cleanup, heap, stack and slices.


## Struct
As we have already learnt c we should be familiar with struct. It is just a way to group multiple type of item together. 

In Rust we are already familiar with tuple which is defined using parenthesis `()`.  But the problem with tuple is we don't know what do first, second data means. It would be convienent if we are able to name those data.

Lets create simple struct.

```

struct User {
	username: String,
	email: String,
	sign_in_count: u64,
	active: bool,
}
```
The key value pair on struct is also called fields. Now using concrete value lets create the instance.


```
let me = User {
	email: String::from("hey@gmail.com"),
	username: String::from("hey jude"),
	active: true,
	sign_in_count: 1,
}
Trailing comma is allowed at end. Order is not important here. And we can use dot notation to get value from struct. `me.email` will give `hey jude`

We can initlize use shortcut like this

let email = String::from("hey@gmail.com")
let username = String::from("hey jude")

let me = User {
	email,
	username,
	active: true,
	sign_in_count: 1,
}

So we used email and username shortcut. 

You can also use one struct to update another struct if they both have things on common like


let me = User {
	email: "hey@judge.com",
	username: "hello",
	active: true,
	sign_in_count:1
}

let similar = User {
	email: "heyyyyy@jude.com",
	username: "hello",
	..me,
}
```

now due to `..me` similar will have active to `true` and `sign_in_count` to `1`. If you are familiar with es6 this may seem too easy ;)

We can create tuple struct too.  Like this

```
struct Student(String, String)
struct Teacher(String, String)
```

If we hadn't use tuple struct and had used tuple only we would be confused if it was student or teacher. Because you cannot know `("hey", "judge")` is student or teacher but you know `Student("hey", "jude")` is definitely a student.

Now its time to debug the struct. As primitive type like integer, boolean implement `Display` trait we can use them directly on `prinln!` macro.

There is different way to debug struct. We may want full syntax, syntax without commans, inline etc. Due to this ambiguity rust don't guess and display error.  We can use `{:?}` instead of `{}` to debug struct. `{:?}` inside `println!` macro means we want to use output format called `Debug`.  But for that you will need to explicitly add annotation to struct like this
```
#[derive(Debug)]

struct User {
	username: String,
	name: String,
}

let user = User {
	username: String::from("hey_jude"),
	name: String::from("Hey Jude"),
}

println!("{:?}", rect1)
```

In console we should see output like this
```
{ username: "hey_jude", name: "Hey Jude"}
```

If you want another variant you can use `{:#?}` instead of `{:?}`.

We can add method to `struct` using `impl` syntax

```
struct User {
	username: String,
	password: String,
}

impl User {
	fn get_details(&self) -> String {
		self.username + " " + self.password
	} 
}

```

You can call method using instance.get_details


Associated functions are useful on rust. The method that don't take `&self` parameter are associated function. We have use this funciton by using `String::from`.

We can call associated function by double colon `::`. Double colon is also used for module namespacing.
