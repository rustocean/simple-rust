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

here x will be 4 . Because `{}` is an expression which is use to create scopes. Remember we didn't used semicolon at end of scope.

In rust you can define return type using  arrow `->`

function summer(x: i32, y: i32) -> i32 {
	x + y
}

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

(1..4) means range which is from `1,3` so you should guess `4` is excluded .

```
// Todo Exercises
```
