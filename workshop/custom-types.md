# Custom Types

We already gave you a sneak peak at defining and using custom types in Substrate.

For our Pallet, we will define a `struct OracleEvent`.

Inside of the `OracleEvent` struct, we have three fields. `event_name` which we will use to track each event name. 
We also store the `event_details` and `timestamp`.

Finally, note that we take advantage of the `#[derive]` macro to implement all the different traits the Pallet expects from these custom types, just as we explained earlier. If you don't include these, the Rust compiler will start yelling at you as soon as you try to use these custom types.

Check your code against the solution and let's move on to adding storage items for our oracle events!

<!-- slide:break-40 -->

<!-- tabs:start -->

#### ** ACTION ITEMS **

Add the following custom types to your Pallet.

```rust
/// Data related to Oracle Event
#[derive(Encode, Decode, Default, TypeInfo, Clone, MaxEncodedLen)]
#[scale_info(skip_type_params(OracleEventLength))]
pub struct OracleEvent<OracleEventLength> {
	pub event_name: BoundedVec<u8, OracleEventLength>,
	pub event_details: BoundedVec<u8, OracleEventLength>,
	pub timestamp: u64,
}

impl<OracleEventLength> OracleEvent<OracleEventLength> {
	pub fn new(
		event_name: BoundedVec<u8, OracleEventLength>,
		event_details: BoundedVec<u8, OracleEventLength>,
		timestamp: u64,
	) -> OracleEvent<OracleEventLength> {
		Self { event_name, event_details, timestamp }
	}
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

	// The basis which we buil
	#[pallet::pallet]
	pub struct Pallet<T>(_);

	/// Data related to Oracle Event
	#[derive(Encode, Decode, Default, TypeInfo, Clone, MaxEncodedLen)]
	#[scale_info(skip_type_params(OracleEventLength))]
	pub struct OracleEvent<OracleEventLength> {
		pub event_name: BoundedVec<u8, OracleEventLength>,
		pub event_details: BoundedVec<u8, OracleEventLength>,
		pub timestamp: u64,
	}

	impl<OracleEventLength> OracleEvent<OracleEventLength> {
		pub fn new(
			event_name: BoundedVec<u8, OracleEventLength>,
			event_details: BoundedVec<u8, OracleEventLength>,
			timestamp: u64,
		) -> OracleEvent<OracleEventLength> {
			Self { event_name, event_details, timestamp }
		}
	}

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

	#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {}

}
```

<!-- tabs:end -->
