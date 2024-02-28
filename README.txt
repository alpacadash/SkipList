You are responsible for implementing the following member functions as part of the SkipList
class in SkipList.hpp:
SkipList()
This is the constructor for the class. You should initialize any variables you add to the class
here. Note from the “Restrictions” section of this document that your implementation must be
a linked structure. Notice that the size of the SkipList is not passed as a parameter. This
means that the SkipList can be arbitrary length, purely determined by how many insertions
are made. This means you will be allocating memory for your SkipList dynamically as
insertions are made.
~SkipList()
This is the destructor for the class. Destructors are called when instances of your class fall out
of scope. This means any memory you dynamically allocated on the heap must be freed here.
Since your SkipList must be a linked structure, as a small hint, you will probably be freeing the
node structures that you allocated during insertion. It’s up to you to traverse your SkipList in a
way that accomplishes this.
Note, if you elect to use shared pointers or unique pointers the compiler will generate code to
deallocate the memory for you if certain conditions are met. You should only use these
features of the standard library if you already understand them or are willing to put in extra
effort. In most industry settings features like these will be used as opposed to explicitly
implemented destructors, unless access to the standard library is limited.
[[nodiscard]] size_t size() const noexcept
Returns the numbers of distinct keys present in the skip list. Note, this is different from the
total number of nodes in your skip list, since you may allocate duplicate nodes for the different
layers.
[[nodiscard]] bool empty() const noexcept
Returns true if the SkipList contains zero keys. False if not.
[[nodiscard]] size_t layers() const noexcept
Returns the number of layers currently present in the skip list including the bottom-most layer
(with all of the keys) and the top-most layer which will contain zero keys. For the sake of this
assignment you will be initializing your skip list with two layers. Other skip list
implementations might initialize it differently, but we need to agree on something consistent for
the sake of grading. When you create a new skip list it should conceptually look like this:
That is, it contains two empty layers:
● Layer S0 which can be thought of as the “base” layer which will contain all of the
distinct keys.
● Layer S1 which can be thought of as the “top” layer (sometimes called “fast lane”)
which will contain no keys.
[[nodiscard]] size_t height(const K& key) const
Returns the height of key k in the skip list. In other words, the highest layer i + 1 for which the
key k is present (assuming the bottom-most layer is considered layer 0). For example, the
height of 5 in the following diagram would be 1, and the height of 7 would be 2. (Note, if you
insert 5 and 7 into your skip list they may end up at layers different from the ones in this
example. The purpose of this example is purely to demonstrate how to return the correct
height number given an existing skip list with 5 and 7 in these positions).
This function should throw std::out_of_range if the key k does not exist in the skip list.
[[nodiscard]] const K& nextKey(const K& key) const
Returns the next largest key in the skip list according to the bottom-most layer if such a key
exists. If this is the largest key in the skip list RuntimeException should be thrown since
there is not a next largest key. For example, in the following skip list:
nextKey(5) would return 7.
nextKey(7) would throw a std::out_of_range because 7 is the largest key.
This function should throw std::out_of_range if the key k does not exist in the skip list.
[[nodiscard]] const K& previousKey(const K& key) const
Returns the next smallest key in the skip list according to the bottom-most layer if such a key
exists. If this is the smallest key in the skip list, RuntimeException should be thrown since
there is not a next smallest key. For example, in the following skip list:
previousKey(5) would throw a RuntimeException because 5 is the smallest key.
previousKey(7) would return 5.
This function should throw std::out_of_range if the key k does not exist in the skip list.
[[nodiscard]] V& find(const K& key)
Returns a modifiable reference to the value mapped to by key k. This could be used if we
want to update an entry in the skip list.
For example, given the following skip list:
std::string& s = sl.find(5);
s[3] = ‘h’;
Would update the skip list like so:
This function should throw std::out_of_range if the key k does not exist in the skip list.
[[nodiscard]] const V& find(const K& key) const
Returns a non-modifiable reference to the value mapped to by key k. This could be used if we
want to access the value of a key for a SkipList object that is marked constant.
This function should throw std::out_of_range if the key k does not exist in the skip list.
bool insert(const K& key, const V& value)
This will be the hardest part of the assignment.
Inserts the key k and the corresponding value v into your skip list. This should result in a
“mapping” between k and v such that I can find the value v by simply calling find(k). The
insertion procedure is quite involved in order to maintain fast lookup times. See the following.
As seen in lecture, an entry always gets added to the bottom-most layer of the skip list. Skip
lists maintain fast lookup times by probabilistically propagating keys into one or more layers
after first inserting into the bottom-most layer. In lecture, we flipped a coin to determine
whether or not a key would also be added to the “next” layer. Since we can’t grade a
non-deterministic solution reliably, we have provided a function to deterministically generate
truth values for you to use when deciding whether or not to propagate an entry to the next
layer. This function is flipCoin. There are extensive comments in the code describing how
flipCoin works, though it is possible to complete the project without understanding it. You
just need to use it when you would normally flip a coin.
One important caveat. Since flipCoin is only implemented for integers and strings, you are
only required to support integer and string keys. Though values can still be any type . You
should test your implementation accordingly.
Since we don’t want you to infinitely flip a coin, there are limits on how many layers your skip
list can have.
● For a skip list with 16 or fewer distinct keys (including the element that was just added
in the bottom-most lane) the maximum number of layers is 13. This includes the
top-most layer (Sh, where h is the height of the skip list) and the base layer S0 . See
InvolvedHeightsTest in SkipTests.cpp for a concrete example.
● For a skip list with more than 16 distinct keys the maximum number of layers is 3 *
ceil(log2(n)) + 1. In other words, the skip list should have a height of 3 *
ceil(log2(n)) in the worst case. The library cmath has a function for logarithms
and ceiling, and you may use this for the assignment. See Capacity17Test in
SkipTests.cpp for a concrete example of this.
If the key k already exists in the skip list, you should return false and not insert anything.
Additionally, it is possible that additional layers may already exist if elements have been
erased, as erasing nodes will not remove layers.
[[nodiscard]] std::vector<K> allKeysInOrder() const
Pretty straightforward. Returns a vector which contains all keys in ascending order (as they
should already be in the base layer of the skip list).
[[nodiscard]] bool isSmallestKey(const K& key) const
Also straightforward. Returns true if the key k is the smallest key in the skip list. False if not.
This function should throw std::out_of_range if the key k does not exist in the skip list.
[[nodiscard]] bool isLargestKey(const K& key) const
Returns true if the key k is the largest key in the skip list. False if not.
This function should throw std::out_of_range if the key k does not exist in the skip list.
void erase(const K& key)
Removes the requested key and the value mapped to key from the skiplist. If the key is not in
the skip list, throw std::out_of_range. Erase will not remove a layer if it is the last or only
element on that layer once removed. Layers should always remain in the SkipList once
created.