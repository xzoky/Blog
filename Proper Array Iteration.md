# `for i in 0...array.count-1 { }`

Why it's bad:
* If `array` is empty, this will try to form the range `0...(-1)`, which will crash:

	> Fatal error: Can't form Range with upperBound < lowerBound
* Too easy to forget the `-1`.
* Doesn't work for sliced collections, where the indices don't start from zero.
* Doesn't work for collections whose indices aren't `Int`.

Slightly improved: `for i in 0..<array.count {`

# `for i in 0..<array.count {`

Improvements over the previous:

* Support empty arrays gracefully (by forming an empty range)
* Can't forget the `-1`

Why it's bad:

* Too easy to accidentally type `0...array.count`, causing an off-by-one error (by trying to access `array[array.count]`)
	* You might think you're immune, but believe me, I've seen tens of StackOverflow questions about this.
* Doesn't work for sliced collections, where the indices don't start from zero.
* Doesn't work for collections whose indices aren't `Int`.

Improved: `for i in array.indices { }`

# `for i in array.indices { }`

[`.indices`](https://developer.apple.com/documentation/swift/collection/1641719-indices) is a computed property that's available not just on `Array`, but on any `Collection`. In the case of `Array`, it simply return `0..<self.count`. However, other types implemented differently, so that it's always correct. For example, slices start and end at the correct index into their parent container.

Improvements over the previous:

* No off-by-one errors
* Works for slices
* Works for non-integer indexed collection
* Very expressive, reads like English.

# Using indices when you don't need to

Iterating the indexes of an array when you don't need to, just introduces unecessary complexity. For example:

``` Swift
let array = ["A", "B", "C" ]
for i in array.indices {
	let char = array[i]
	print(char)
}
```

In this example, the indices arne't what's needed. They're only used as a means to an end, to get at the characters. Instead, just iterate over the characters directly:

``` Swift
let array = ["A", "B", "C" ]
for char in array {
	print(char)
}
```

However, there are times when you *do* need the index, such as when you want to use it (e.g. print it for debug informatin) or use it to mutate the original array. For example:


``` Swift
var array = ["A", "B", "C" ]
for i in array.indices {
	let char = array[i]
	print(char)
	array[i] = " "
}
```

But even this isn't the best way to do it! Instead, use `Sequence.enumerated()`.

# `Sequence.enumerated()`

[`Sequence.enumerated()`](https://developer.apple.com/documentation/swift/sequence/1641222-enumerated) is a method for iterating both the indices and the elements of any sequence (not just `Array`). The indices aren't necessarily `Int`, but they are guarenteed to be valid indices that can be used to retrieve values (via the subscript operator).

``` Swift
var array = ["A", "B", "C" ]
for (i, char) in array.enumerated() {
	let char = array[i]
	print(char)
	array[i] = " "
}
```


# Summary

There are 3 ways to correctly iterate a sequence in a `for` loop, depending on whether you need only the indices, only the elements, or both the indices and the elements.

It's actually exceptionally rare that you need to iterate over *only* the indices of a collection, and not the associated values. Thus, you'll almost always be using `for element in array { }` or `for (offset, element) in array.enumerated() { }`


|					 | Indices not needed  | Indices needed |
|---------------------|---------------------|----------------|
| Elements not needed | Just don't iterate anything lol | `for i in array.indices { }` |
| Elements needed	 | `for element in array { }`  | `for (offset, element) in array.enumerated() { }` |