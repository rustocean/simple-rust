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