# Sequences
### IN SWIFT

---

## What are Sequences?

Swift defines the `SequenceType` as
> A type that can be iterated with a for...in loop.

---

```swift
for element in mySequence {
    // do something
}
```

---

## Under the hood

```swift
let generator = mySequence.generate()
while let element = generator.next() {
  // do something
}
```

---

`mySequence.generate()` 🤔

---

`func generate() -> Self.Generator`

---

# `SequenceType's have a GeneratorType`
`GenoratorTypes` are responsible for Encapsulating iteration state and interface for iteration over a sequence.

---

# Generators

---

```swift
protocol GeneratorType {
    typealias Element
    mutating func next() -> Self.Element?
}
```

---

```swift
struct IntSequence: SequenceType {

    // Define the generator of the sequence
    struct Generator: GeneratorType {
        var value: Int // encapsulated state

        init(initialValue: Int) {
            value = initialValue
        }

        // returns the next value in the sequence
        mutating func next() -> Int? {
            return value++
        }
    }

    func generate() -> Generator {
        return Generator(initialValue: 0)
    }
}

```
---

### usage

```swift
let mySequence = IntSequence()
for element in mySequence {
    print(element)
}

// 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24...


```

---

## Simplified
`AnyGenerator<T>`

```swift
struct IntSequence: SequenceType {
    private var initialValue: Int

    init(initialValue: Int = 0) {
        self.initialValue = initialValue
    }

    func generate() -> AnyGenerator<Int> {
        var closedValue = initialValue
        return anyGenerator { return closedValue++ }
    }
}
```