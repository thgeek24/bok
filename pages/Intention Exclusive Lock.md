- What
	- It is used at table-level and does not actually place a lock on a table
	- It is used to indicate to other transactions the intention to update/insert specific rows within the table
	- A IX lock must be acquired before a thread acquires an X lock (to update/insert rows within a table)
- Why
- How
	- Table of Lock Compatibility
		- |Lock Type|IS|IX|S|X|
		  |IS|yes|yes|yes|no|
		  |IX|yes|yes|**no**|no|
		  |S|yes|**no**|yes|no|
		  |X|no|no|no|no|
- How Good
- Refs
- See Also