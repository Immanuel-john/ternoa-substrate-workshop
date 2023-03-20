# Custom Storage

Our Pallet will use 3 storage items to track all of the state.

1. A `StorageValue` named `OracleEventFeed` which will keep track of all the events in the Pallet.
	* This `StorageValue` simply keeps track of a `BoundedVec<OracleEvent>` where all the event feed will be appended. 
	* By using a `BoundedVec`, we ensure that each storage item has a maximum length, which is important for managing limits within the runtime.
	* It is worth noting the `ValueQuery` configuration in the `StorageValue`. This basically assumes if there is no value in storage, for example at the start of the network, that we should return the default value rather than an option `None`.


<!-- slide:break-40 -->

<!-- tabs:start -->

#### ** ACTION ITEMS **

Add the following custom storage item to your Pallet.

```rust
/// Oracle Event Feed for storing event details posted by root account
#[pallet::storage]
#[pallet::getter(fn oracle_event_feed)]
pub type OracleEventFeed<T: Config> = StorageValue<
	_,
	BoundedVec<OracleEvent<T::OracleEventLength>, T::OracleEventLength>,
	ValueQuery,
>;
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

	/// Oracle Event Feed for storing event details posted by root account
	#[pallet::storage]
	#[pallet::getter(fn oracle_event_feed)]
	pub type OracleEventFeed<T: Config> = StorageValue<
		_,
		BoundedVec<OracleEvent<T::OracleEventLength>, T::OracleEventLength>,
		ValueQuery,
	>;

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
