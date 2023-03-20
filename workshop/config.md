# Configuring Your Pallet

To build our pallet, we need to include some custom configurations which will allow our pallet to gain access to outside interfaces like:

* UnixTime from pallet timestamp so that we can clear up events which are residing more than the maximum time. 
* Setting limits for the event feed length.
* Setting limits for maximum time the event can be stored on our blockchain.

We will introduce these to the `trait Config` for our Pallet.

To do this, this we use a few different tools:

* `UnixTime`: A trait that gives access to get timestamp functionalities to this pallet.
* `Get<u32>`: A trait which simply fetches a `u32` value, allowing the user to configure the `OracleEventLength`.
* `Get<u32>`: A trait which simply fetches a `u64` value, allowing the user to configure the `MaxTimeForEvents`.

We will use these interfaces in the future, but a sneak peak to how you might actually see these used in the code:

```rust
// Get the current time in seconds
let now: u64 = T::TimeProvider::now().as_secs();

// Get the `MaxTimeForEvents` limit.
let max_time: u32 = T::MaxTimeForEvents::get();

```

<!-- slide:break -->

<!-- tabs:start -->

#### ** ACTION ITEMS **

Import the `UnixTime` trait to your project:

```rust
use frame_support::{traits::UnixTime};
```

Then, update your `trait Config` to have the following:

```rust
// Your Pallet's configuration trait, representing custom external types and interfaces.
#[pallet::config]
pub trait Config: frame_system::Config {
	/// Because this pallet emits events, it depends on the runtime's definition of an event.
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

#### ** SOLUTION **

This should compile successfully by running:

```bash
cargo build -p pallet-template
```

Don't worry about warnings.

```rust
#![cfg_attr(not(feature = "std"), no_std)]

pub use pallet::*;

#[frame_support::pallet]
pub mod pallet {
	use frame_support::pallet_prelude::*;
	use frame_system::pallet_prelude::*;

	use frame_support::{traits::UnixTime};

	// The struct on which we build all of our Pallet logic.
	#[pallet::pallet]
	pub struct Pallet<T>(_);

	/* Placeholder for defining custom types. */

	/* Placeholder for defining custom storage items. */

	// Your Pallet's configuration trait, representing custom external types and interfaces.
	#[pallet::config]
	pub trait Config: frame_system::Config {
		/// Because this pallet emits events, it depends on the runtime's definition of an event.
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

	// Your Pallet's events.
	#[pallet::event]
	#[pallet::generate_deposit(pub(super) fn deposit_event)]
	pub enum Event<T: Config> {}

	// Your Pallet's error messages.
	#[pallet::error]
	pub enum Error<T> {}

	// Your Pallet's callable functions.
	#[pallet::call]
	impl<T: Config> Pallet<T> {}

	// Your Pallet's internal functions.
	impl<T: Config> Pallet<T> {}
}
```

<!-- tabs:end -->
