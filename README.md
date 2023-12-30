# UUID-comp

This is a non-AI generated summary of the different unique identifiers that can be used for a  database, 5 unique ( ! ) challengers imo:
* UUID v4
* UUID v7
* ULID
* TSID
* Sonyflake

Make a PR if you wish to submit your own algorithms or a decent one that I forgot.

---

### [UUID Version 4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random))

```
uuid().v4 // 
```

* A version 4 UUID is randomly generated.
* As in other UUIDs, 4 bits are used to indicate version 4, and 2 or 3 bits to indicate the variant (102 or 1102 for variants 1 and 2 respectively). Thus, for variant 1 (that is, most UUIDs) a random version-4 UUID will have 6 predetermined variant and version bits, leaving 122 bits for the randomly generated part, for a total of 2122, or 5.3×1036 (5.3 undecillion) possible version-4 variant-1 UUIDs.
* There are half as many possible version-4 variant-2 UUIDs (legacy GUIDs) because there is one less random bit available, 3 bits being consumed for the variant.


#### Collision

Collision occurs when the same UUID is generated more than once and assigned to different referents. In the case of standard version-1 and version-2 UUIDs using unique MAC addresses from network cards, collisions are unlikely to occur, with an increased possibility only when an implementation varies from the standards, either inadvertently or intentionally.

In contrast to version-1 and version-2 UUIDs generated using MAC addresses, with version-1 and -2 UUIDs which use randomly generated node ids, hash-based version-3 and version-5 UUIDs, and random version-4 UUIDs, collisions can occur even without implementation problems, albeit with a probability so small that it can normally be ignored. This probability can be computed precisely based on analysis of the birthday problem.[21]

For example, the number of random version-4 UUIDs which need to be generated in order to have a 50% probability of at least one collision is 2.71 quintillion, computed as follows:[22]

 ![alt text](https://wikimedia.org/api/rest_v1/media/math/render/svg/d765375604fa68b3179d171917404a6361f7f3d1)

This number is equivalent to generating 1 billion UUIDs per second for about 86 years. A file containing this many UUIDs, at 16 bytes per UUID, would be about 45 exabytes.

The smallest number of version-4 UUIDs which must be generated for the probability of finding a collision to be p is approximated by the formula

 ![alt text](https://wikimedia.org/api/rest_v1/media/math/render/svg/1899b35f447e21570910040c416325853ca1ac3f)

> Thus, the probability to find a duplicate within 103 trillion version-4 UUIDs is one in a billion.

---

### [UUID version 7](https://uuid.ramsey.dev/en/stable/rfc4122/version7.html)

Version 7 UUIDs are binary-compatible with ULIDs (universally unique lexicographically-sortable identifiers).

Both use a 48-bit timestamp in milliseconds since the Unix Epoch, filling the rest with random data. Version 7 UUIDs then add the version and variant bits required by the UUID specification, which reduces the randomness from 80 bits to 74. Otherwise, they are identical.

Version 7 UUIDs solve two problems that have long existed with the use of version 1 UUIDs:
* Scattered database records
* Inability to sort by an identifier in a meaningful way (i.e., insert order)

Version 7 UUIDs combine random data (like version 4 UUIDs) with a timestamp (in milliseconds since the Unix Epoch, i.e., 1970-01-01 00:00:00 UTC) to create a monotonically increasing, sortable UUID that doesn’t have any privacy concerns, since it doesn’t include a MAC address.

#### [Collision](https://math.stackexchange.com/questions/4697032/threshold-for-the-number-of-uuids-generated-per-millisecond-at-which-the-colli)

> Based on the results above, I conclude that as long as we generate at least g=2 UUIDs per millisecond, the cutoff point will be at least 140737488355329 ms, which comes out to 4459.7 years. If we are generating several UUIDs per second then the cutoff point will be around 281474976710656 ms or 8919.4 years.
> In other words, if you are only focused on avoiding hash collisions then you should pretty much always choose v4 instead of v7. There may be other reasons for using UUID v7 though; for example, v7 will generate nearby UUIDs during each millisecond which can help with database index locality and thus improve runtime (see e.g. source). Overall, neither version is a strict upgrade over the other, and you just have to consider the tradeoffs when choosing a UUID algorithm for your specific application.

---

### [ULID](https://github.com/ulid/spec?tab=readme-ov-file)

```
ulid() // 01ARZ3NDEKTSV4RRFFQ69G5FAV
```

* 128-bit compatibility with UUID
* 1.21e+24 unique ULIDs per millisecond
* Lexicographically sortable!
* Canonically encoded as a 26 character string, as opposed to the 36 character UUID
* Uses Crockford's base32 for better efficiency and readability (5 bits per character, resulting in a 26-character ULID string)
* Case insensitive
* No special characters (URL safe)
* Monotonic sort order (correctly detects and handles the same millisecond)

---

### TSID

```
tsid() // generate 4 times:
522,836,310,860,897,560	fourth
522,836,310,861,131,029	first
522,836,310,862,691,606	second
522,836,310,863,962,391	third
```

Some of the key features are:
* TSIDs are generated in time-sortable order (as with other time-sorted identifiers, including the aforementioned UUIDv7)
* TSIDs are a 64 bit integer
* TSIDs can be represented as a 13 character string through Crockford base32 encoding
* The TSID generation algorithm can optionally include node IDs, to ensure that TSIDs generated at multiple sources (e.g. multiple databases or application servers) stays unique

[Interesting Read](https://www.foxhound.systems/blog/time-sorted-unique-identifiers/)
[Discussion](https://www.reddit.com/r/programming/comments/18mzlzc/tsids_strike_the_perfect_balance_between_integers/)

---

### [Sonyflake](https://github.com/sony/sonyflake)

> Sonyflake is a distributed unique ID generator inspired by Twitter's Snowflake.

> Sonyflake focuses on lifetime and performance on many host/core environment. So it has a different bit assignment from Snowflake. A Sonyflake ID is composed of

* 39 bits for time in units of 10 msec
*  8 bits for a sequence number
* 16 bits for a machine id
* As a result, Sonyflake has the following advantages and disadvantages:

> The lifetime (174 years) is longer than that of Snowflake (69 years)
It can work in more distributed machines (2^16) than Snowflake (2^10)
It can generate 2^8 IDs per 10 msec at most in a single machine/thread (slower than Snowflake)
However, if you want more generation rate in a single host, you can easily run multiple Sonyflake ID generators concurrently using goroutines.

--- 

### Summary

| Name        | Length (bits)         | char len as URL safe string (Crockford) | Collision Probability | Lifetime (years) | Chronological Sorting |
| ------------- |:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|
| UUID V4 | 128 | 26 | Lowest: one in a billion UUID v4 within 103 trillion generated | No practical limit | No |
| UUID V7 | 128 | 26 | one out of 281,474,976,710,656 in a given millisecond | No practical limit | No |
| ULID | 128 | 26 | one out of 1,208,925,819,614,629,174,706,176 in a given millisecond | No practical limit | Yes time-sorted |
| TSID | 64 | 13 | Higher: At least a billion identifiers per millisecond before the risk appears | 70 | Yes time-sorted |
| Sonyflake | 64 | 13 | | 174 | No |

---

### Conclusion (personal thoughts)

Overall TSID has my preference for most usecases, especially it looks great to attribute unique IDs for messages as the timestamp is embedded within the unique identifier. (which means you can discard the timestamp as well, so you replace the timestamp which is 128 bits, and the UUID which is also 128 bits, with only 64 bits.
