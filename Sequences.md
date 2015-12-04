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

# Example

---

```swift
public struct Deck {
    private var cards: [PlayingCard]

    public static func standard52CardDeck() -> Deck {
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

    public init(_ cards: [PlayingCard]) {
        self.cards = cards
    }

    public mutating func shuffle() {
        cards.shuffleInPlace()
    }

    public mutating func deal() -> PlayingCard? {
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
public struct Deck: SequenceType {
    ...
}
```

---

```swift
public struct DeckGenerator: GeneratorType {
    private var cards: [PlayingCard]
    private var index = 0

    init(cards: [PlayingCard]) {
        self.cards = cards
    }

    public mutating func next() -> PlayingCard? {
        guard index < cards.count else { return .None }
        return cards[index++]
    }
}
```

---

```swift
public struct Deck: SequenceType {
    public func generate() -> DeckGenerator {
        return DeckGenerator(cards: cards)
    }
}
```
