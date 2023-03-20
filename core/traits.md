# Rust Traits

Traits tell the Rust compiler what kinds of functionality a type has.
Traits are similar to a feature often called interfaces in other languages, although with some differences.

```rust
trait Opposite {
	fn opposite(&self) -> Self;
}

#[derive(PartialEq, Eq)]
enum Direction {
	Up,
	Down,
	Left,
	Right,
}

impl Opposite for Direction {
    fn opposite(&self) -> Direction {
        match self {
            Direction::Up => Direction::Down,
            Direction::Down => Direction::Up,
            Direction::Left => Direction::Right,
            Direction::Right => Direction::Left,
        }
    }
}
```

With this trait implemented, we can do things like:

```rust
assert!(Direction::Left.opposite() == Direction::Right)
```

<!-- slide:break -->

# Advanced Traits

You can also define custom traits, and associate things like type definitions, which themselves have traits.

Every Pallet in Substrate has a `trait Config`, which allows you to define the types and interfaces that your Pallet depends on.

This trait always inherits from the `frame_system::Config` which defines all the core types in your runtime, like:

* `T::AccountId`: The type representing a user address.
* `T::BlockNumber`: The type representing a block number.
* etc...

These traits allows your runtime to be **generic**, which means that you can easily change the fundamental types in your blockchain, and things will still work.


Here is a hint as to what you might see later in the tutorial, but practically speaking, this is a pretty advanced concept, so mostly take it for granted.

```rust
#[pallet::config]
pub trait Config: frame_system::Config {
	// The type used across your runtime for emitting events.
	type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;

	///Time provider for getting timestamp
	type TimeProvider: UnixTime;

	/// Maximum length for Oracle Event.
	#[pallet::constant]
	type OracleEventLength: Get<u32>;

	/// Maximum time for storing an Oracle Event.
	#[pallet::constant]
	type MaxTimeForEvents: Get<u64>;
}
```
