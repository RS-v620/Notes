#hashing

Hash Function is any function that can be used to map data of `arbitrary` size to another fixed-size/variable-size value that takes away the uncertainity of the arbitrary input.
The values returned can be called *hash values*, *hash codes*, *digests* or simply *hashes*.
These values are often used to index a fixed-size table called a *hash table*.
Unlike lists or trees, has based addressing into hash-tables provides near-constant time data retrieval. 
A good hash function satisfies two basic properties:
- it should be very fast to compute, and
- it should minimize duplication of output values ([collisions](https://en.wikipedia.org/wiki/Hash_collision "Hash collision")). 

Hash functions rely on generating favorable [probability distributions](https://en.wikipedia.org/wiki/Probability_distribution "Probability distribution") for their effectiveness, reducing access time to nearly constant.