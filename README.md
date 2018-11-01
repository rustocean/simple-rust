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
$ No I don't want to uninstall it

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