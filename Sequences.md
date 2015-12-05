# Sequences
### IN SWIFT

---

## What are Sequences?

Swift defines the `SequenceType` as
> A type that can be iterated with a for...in loop.

---

```swift
for element in mySequence {
    ...
}
```

---

## Under the hood

```swift
let generator = mySequence.generate()
while let element = generator.next() {
    ...
}
```

---

`mySequence.generate()` 🤔

---

```swift
protocol SequenceType {
    typealias Generator: GeneratorType
    func generate() -> Generator
}
```

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

# Example

---

```swift
struct Deck {
    private var cards: [PlayingCard]

    static func standard52CardDeck() -> Deck {
        let suits: [Suit] = [.Spades, .Hearts, .Diamonds, .Clubs]
        let ranks: [Rank] = [.Ace, .Two, .Three, .Four, .Five, .Six, .Seven, .Eight, .Nine, .Ten, .Jack, .Queen, .King]

        var cards: [PlayingCard] = []
        for suit in suits {
            for rank in ranks {
                cards.append(PlayingCard(rank: rank, suit: suit))
            }
        }

        return Deck(cards)
    }

    init(_ cards: [PlayingCard]) {
        self.cards = cards
    }

    mutating func shuffle() {
        cards.shuffleInPlace()
    }

    mutating func deal() -> PlayingCard? {
        guard !cards.isEmpty else { return nil }

        return cards.removeLast()
    }
}
```

###### `https://github.com/apple/example-package-deckofplayingcards`

---

```swift
let numberOfCards = 10
var deck = Deck.standard52CardDeck()

for _ in 1...numberOfCards {
    guard let card = deck.deal() else {
        print("No More Cards!")
        break
    }
    print(card)
}
```

---

```swift
struct Deck: SequenceType {
    ...
}
```

---

```swift
struct DeckGenerator: GeneratorType {
    private var cards: [PlayingCard]
    private var index = 0

    init(cards: [PlayingCard]) {
        self.cards = cards
    }

    mutating func next() -> PlayingCard? {
        guard index < cards.count else { return .None }
        return cards[index++]
    }
}
```

---

```swift
struct Deck: SequenceType {
    func generate() -> DeckGenerator {
        return DeckGenerator(cards: cards)
    }
}
```

---

### usage

```swift
let deck = Deck.standard52CardDeck()
deck.prefix(10).forEach { card in
    ...
}
```

---

## With `SequenceType` you get all of the powerful methods you get with `Array` and `Dicitonary`

> `map, filter, forEach, dropFirst, dropLast, prefix, suffix, split`

---

## AnyGenerator<T>

---

```swift
struct Deck: SequenceType {
    func generate() -> DeckGenerator {
        return DeckGenerator(cards: cards)
    }
}
```

---

```swift
struct Deck: SequenceType {
    func generate() -> AnyGenerator<PlayingCard> {
        var index = 0

        // pass it a "next" closure
        return anyGenerator {
            guard index < self.cards.count else { return .None }
            return self.cards[index++]
        }
    }
}
```

---

## CollectionType

```swift
extension Deck: CollectionType {
    var startIndex: Int { return 0 }
    var endIndex: Int { return cards.count }

    public subscript(i: Int) -> PlayingCard {
        return cards[i]
    }
}
```

---

> a collection is multi-pass: any element may be revisited merely by saving its index.<br><br><br><br><br><br>

```swift
// is the same as `for card in deck {}:`
for i in deck.startIndex..<deck.endIndex {
    let card = deck[i]
    ...
}
```

---

## AnyGenerator<T>

---

```swift
var positiveNumbers: AnyGenerator<Int> {
    var i = 0
    return anyGenerator { return i++ }
}

for i in positiveNumbers {
    print(i)
}
```


