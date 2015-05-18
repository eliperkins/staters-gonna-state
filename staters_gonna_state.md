# [fit] State

^ State... _pause_

^ We've all had times where we've been reading code and said "it's hard to follow this code, I've got a variable set over here, unset over here, mutated here"

^ ..or "I wonder what would happen if this switch was flipped and not that one, this path followed without this set..." and so on

---

![](images/taylor_swift_holding.jpg)

^ We start out simple, just juggling one thing at a time, one feature at a time, one process at a time.

^ But as we add on more, we need to think about more things. About more objects, more functions and methods, a couple more `isLoading` and `hasFloopedThePig` booleans are added.

^ We end up with more and more state.

---

![fit](images/taylor_swift_dropping.jpg)

^ ...and we get to a point where we just can't juggle all these things in our head, or in our code. We spend time flipping through headers and documentation just to make sense of what is up and what is down.

---

```objc
@property (nonatomic, assign) BOOL loading;

- (void)fetchBananas {
  // Fetch some bananas from the network.
}
```

^ Lets consider this example. We just want to fetch some bananas from the network.

^ We'll have a boolean to track if we're loading or not

---

```objc
@property (nonatomic, assign) BOOL loading;

- (void)fetchBananas {
  self.loading = @YES;
  [[APIClient sharedClient] getBananasWithCompletion:^{
    self.loading = @NO;
  }];
}
```

^ An implementation might be trivial, maybe something like this.

---

```objc
@property (nonatomic, assign) BOOL loading;

- (void)fetchBananas {
  self.loading = @YES;
  [[APIClient sharedClient] getBananasWithCompletion:^{
    self.loading = @NO;
  }];
}

- (void)fetchBushels {
  self.loading = @YES;
  [[APIClient sharedClient] getBushelsWithCompletion:^{
    self.loading = @NO;
  }];
}

```

^ But then our application grows and we add another request in here, tracking loading

---

```objc
@property (nonatomic, assign) BOOL loading;

- (void)fetchBananas {
  self.loading = @YES;
  [[APIClient sharedClient] getBananasWithCompletion:^{
    self.loading = @NO;
  }];
}

- (void)fetchBushels {
  self.loading = @YES;
  [[APIClient sharedClient] getBushelsWithCompletion:^{
    self.loading = @NO;
  }];
}

```

### self.loading = ???

^ We end up with a bit of a race condition, what if both these are called? One will set loading to false before the other does.

^ What if _SOMEONE ELSE_ sets `loading` to `@NO` before we finish?

---

```objc
@property (nonatomic, assign) BOOL bananasLoading;
@property (nonatomic, assign) BOOL bushelsLoading;

- (void)fetchBananas {
  self.bananasLoading = @YES;
  [[APIClient sharedClient] getBananasWithCompletion:^{
    self.bananasLoading = @NO;
  }];
}

- (void)fetchBushels {
  self.bushelsLoading = @YES;
  [[APIClient sharedClient] getBushelsWithCompletion:^{
    self.bushelsLoading = @NO;
  }];
}

```

---

```objc
@property (nonatomic, assign) BOOL bananasLoading;
@property (nonatomic, assign) BOOL bushelsLoading;
@property (nonatomic, assign) BOOL monkeysLoading;

- (void)fetchBananas {
  ...
}

- (void)fetchBushels {
  ...
}

- (void)fetchMonkeys {
  ...
}

```

# As we get more complex, we add more and more state in

---

## State is exponential

### 1 boolean = 2 states
### 2 booleans = 4 states
### 3 booleans = 8 states
### 4 booleans = 16 states
### ??? booleans = ?!?! states

^ As our application grows, we have an exponential growth in the states we could have for any one given chunk of code.

^ 1 boolean, YES or NO
^ 2 booleans, YES && YES, YES && NO, NO && YES, NO && NO

---

# [fit] State

^ State. _pause_ Let's face it.

---

> State is never simple.
-- Rich Hickey

^ State sucks.

^ "State is never simple"

^ Managing state within applications is difficult.

---

# [fit] Staters Gonna
# [fit] State

^ I'm here to talk about state and help you commiserate and cope with the big s-word.

---

# Eli Perkins

^ I'm Eli Perkins

---

# @eliperkins

^ You can find me most places on the internet as @eliperkins

---

![fit](images/faux_eliperkins.png)

^ ...except on Twitter, this gentleman @eliperkins

---

# [fit] @eliperkins
>most places on the internet

# [fit] @_eliperkins
> on Twitter

^ and you can find me at underscore eliperkins

---

![](images/venmo_logo.png)

^ I work at Venmo on the iOS Team

---

![](images/call_me_maybe.gif)

^ If you want to have fun like these guys dancing to Call Me Maybe, come talk to me afterwards.

---

# [fit] State

^ State.

^ Throughout this talk I wanted to help equip you with some tools and techniques to help you deal with state as you go about developing your applications.

---


# [fit] Simple
## vs.
# [fit] Easy

^ I wanted to start off with a couple definitions.

---

# [fit] Simple

^ What is simple then?

---

# [fit] :construction_worker:

^ Simple fulfill one task, one job, one concept
^ To have focus
^ Does not mean that you can only have one instance (singleton)

---

# [fit] Easy

^ What is easy?

---

# [fit] :wave:

^ Close to hand, familiar
^ near to your understanding or skillset or capabilities
^ "Can I read that?"

---

### `[[[[self tableView] frame] size] height]`

^ To many developers, this is unfamiliar, it's not easy

^ Brackets are scary!

---

### `sum' :: (Num a) => [a] -> a`
### `sum' xs = foldl (\acc x -> acc + x) 0 xs`

^ And to a lot of Objective-C and Swift developers, this too is scary!

^ Sum of all digits in an array

---

### `(defn inc-each [coll] (into (empty coll) (map inc coll)))`

^ And this is scary too! But this is partially, my own fault, since it should be on multiple lines

^ But it's really trivial, it just increments each integer in the collection, creates a new collection

---

## Easy, but not Simple

^ And the inverse is true too.

^ Just because something is easy, doesn't not inherently make it simple

---

## Easy, but not Simple

```swift

class HelperClass {
    class func decorateMonkey(
      monkey: Monkey,
      shouldTellServer: Bool,
      bananasToEatAfter: [Banana]
      ) -> Void {
        ...
      }
}
```

^ Helper class syndrome can introduce complexity, intertwining too many different things

^ While this may be easy ("I only need to bring in one class!"), the effects of a method like make it not simple.

---

# [fit] Simple
## vs.
# [fit] Easy

^ I challenge you to push your boundaries a bit with some of the concepts we talk about here, as they might not be familiar or close to hand, but I hope to prove to you the benefit some niceties that functional programming has to offer.

^ If you leave this talk without remembering anything I said, it's completely fine, as long as you go try something new, like an new language like Haskell or Clojure

^ Or a new programming paradigm like MVVM or functional programming

^ See how it challenges your understanding of programming

---

## Immutability
## Source of Truth
## Value vs. Reference types
## Getting `func`y

^ Some topics we'll cover include:
- The immense value of immutability
- Finding a source of truth in solving your problem
- Value vs reference types in Swift and how they can reduce state, leveraging data
- How functional paradigms can help reduce state

^ We've got some ground to cover, so lets get rolling.

---

## [fit] Immutability
### Source of Truth
### Value vs. Reference types
### Getting `func`y

---

> "Mutable objects complect time and values."
-- Lee Byron

^ @leeb @ React.js Conf 2015

^ I wanted to start with this quote from Lee Byron
^ Lee works on some awesome products at Facebook like React, GraphQL and Immutable.js

^ Intertwining values changing over time

---

![](images/swift.png)

^ Last year, Apple gave us this really cool new thing called Swift.

---

![](images/swift.png)

# [fit] Cool!

---

```swift
let bananas = [Banana(peeled: true), Banana(peeled: false)]
let numberOfBananas = bananas.count
numberOfBananas = 4  // cannot assign to 'let' value
```

^ With Swift, we've gained some great tools to help facilitate immutability

^ Using `let`, we can create constant values inside our applications

^ Our compiler will stop us from making mistakes based around the intent of our variables

---

# Immutabilty helps us
# [fit] reason about code

^ With compiler-level errors and contracts like this, we can begin to make more sense about our code

^ And not only with trival things like counts of arrays

---

```swift
struct HTTPClient {
    let accessToken: AccessToken

    init(accessToken: AccessToken) {
        self.accessToken = accessToken
    }
}
```

^ But even for more complex problems like networking

^ We can begin to create data flow within our applications that helps derive a source of truth.

^ In this examples, we require an access token to instantiate our API client.

^ This is really powerful in that we can no longer have to wonder if we have an authenticated HTTP client.

^ Our access token is required to create one and cannot change.

^ This, we can can reason about very logically.

---

> "What is the impact of the change I want to make?"

---

> If you can't reason about the software, you make it harder to make these decisions.
-- Rich Hickey

^ Leveraging immutability helps understand and reason about our code.

^ Remove the complexity of time and values over time

^ I propose that by designing with immutable data structures in mind, you will be able to confidently make decisions about data flow in your application with fewer concerns.

---

### Immutability
## [fit] Source of Truth
### Value vs. Reference types
### Getting `func`y

^ Moving on, let's talk about finding the source of truth in your applications

---

$$
\newcommand{\ra}[1]{\kern-1.5ex\xrightarrow{\ \ #1\ \ }\phantom{}\kern-1.5ex}
\newcommand{\ras}[1]{\kern-1.5ex\xrightarrow{\ \ \smash{#1}\ \ }\phantom{}\kern-1.5ex}
\newcommand{\da}[1]{\bigg\downarrow\raise.5ex\rlap{\scriptstyle#1}}
\begin{array}{c}
\ra{} & A & \ra{} & B & \ra{} & C & \ra{} & D \\
\end{array}
$$

^ As data flows through your application, you must decide where your source of truth lies.

---

$$
\newcommand{\ra}[1]{\kern-1.5ex\xrightarrow{\ \ #1\ \ }\phantom{}\kern-1.5ex}
\newcommand{\ras}[1]{\kern-1.5ex\xrightarrow{\ \ \smash{#1}\ \ }\phantom{}\kern-1.5ex}
\newcommand{\da}[1]{\bigg\downarrow\raise.5ex\rlap{\scriptstyle#1}}
\begin{array}{c}
\ra{} & A & \ra{} & B & \ra{} & C & \ra{} & D \\
\ra{} & A & \ra{} & C \\
\ra{} & C & \ra{} & B \\
\ra{} & D & \ra{} & B \\
\end{array}
$$

^ As you add delegate methods on, or create new notifications for new changes in data or user interaction, new bindings, new blocks to pass objects around... we add more and more lines.

^ We complicate things by not finding the real source of truth in our applications

^ We derive new connections, new nodes to our data graph, we add complexity

---

$$
\newcommand{\ra}[1]{\kern-1.5ex\xrightarrow{\ \ #1\ \ }\phantom{}\kern-1.5ex}
\newcommand{\ras}[1]{\kern-1.5ex\xrightarrow{\ \ \smash{#1}\ \ }\phantom{}\kern-1.5ex}
\newcommand{\da}[1]{\bigg\downarrow\raise.5ex\rlap{\scriptstyle#1}}
\begin{array}{c}
\ra{} & [Artist] & \ra{} & Artist :: [Album] & \ra{} & Album :: [Song] & \ra{} & Song \\
\end{array}
$$

^ Consider the flow of data for a potential music app.

^ A list of artists, selecting one, down to a single artist, selecting an album, down to a single song

^ This flow of information through our app can help us make decisions about what the source of truth is

^ Note that here in this diagram, I drew the connections between the data with one way arrows.

^ Sure, your Song may know what album it's from, but does it need to communicate it's state up to the album model?

---

$$
\newcommand{\ra}[1]{\kern-1.5ex\xrightarrow{\ \ #1\ \ }\phantom{}\kern-1.5ex}
\newcommand{\ras}[1]{\kern-1.5ex\xrightarrow{\ \ \smash{#1}\ \ }\phantom{}\kern-1.5ex}
\newcommand{\da}[1]{\bigg\downarrow\raise.5ex\rlap{\scriptstyle#1}}
\begin{array}{c}
\ra{} & [Artist] & \ra{} & Artist :: [Album] & \ra{} & Album :: [Song] & \ra{} & Song \\
\end{array}
$$

^ One great benefit we get from this as well is the ability to modularize and reason about smaller changes in our data.

^ If the Album data for a single, specific album we're to change, do we need to reload the whole artist list or even the whole album list?

---

# Truth as State

^ As we tackle this problem, we can begin to reason about our truth as a function of the state of our view.

^ If the source of truth for our Song view is a Song model, let's make sure we update the view when the song model changes

^ If we ever need to consider the changes of an Album for updating a Song view, we've placed our belief of the source of truth in the wrong place

---

# `setState()`

^ Libraries like React really drive this point home. Leveraging fast reconciliation on immutable models, we can find the key point in our data where truth lies, as state.

^ We can begin to reason about the changes and how to derive and reuse views based on this state.

^ Views become a pure function of your state. Given the same state (your input), you'll always get the same output (your views)

---

# View Models

^ Another way to reason about this without a framework like React might be view models

---

```swift
struct SongViewModel {
  let song: Song
}

struct AlbumViewModel {
  let songs: [SongViewModel]
  let imageURL: NSURL

  var image: UIImage {
    // Load our image from the network or cache
  }
}

```

^ Consider a couple of view models here

^ For those not familiar, view models function as a layer between our model and our view

^ We can compose the data from multiple sources to create values which closer represent how our data is displayed

---

```swift
struct SongListViewModel {
  let songs: [SongCellViewModel]
}

struct SongCellViewModel {
  let songViewModel: SongViewModel
  let albumViewModel: AlbumViewModel

  var image: UIImage {
    return albumViewModel.image
  }
}
```

^ Composing both the information around how to display a song and an album together, we get the opportunity to reason about what to views to change when changes are made to our data

^ If an album's image changes, we don't need to update the views for the title of our song, or relayout the play/pause button just for a single song

^ We can reason better about our code, about our data, and about the state of our application

---

$$
\newcommand{\ra}[1]{\kern-1.5ex\xrightarrow{\ \ #1\ \ }\phantom{}\kern-1.5ex}
\newcommand{\ras}[1]{\kern-1.5ex\xrightarrow{\ \ \smash{#1}\ \ }\phantom{}\kern-1.5ex}
\newcommand{\da}[1]{\bigg\downarrow\raise.5ex\rlap{\scriptstyle#1}}
\begin{array}{c}
\ra{} & [ArtistViewModel] & \ra{} & ArtistViewModel :: [AlbumViewModel] \\
\ra{} &  ArtistViewModel :: [AlbumViewModel] & \ra{} & AlbumViewModel :: [SongCellViewModel] \\
\ra{} &  SongCellViewModel :: (AlbumViewModel, SongViewModel) & \ra{} & SongViewModel \\
\end{array}
$$

^ One great benefit we get from this as well is the ability to modularize and reason about smaller changes in our data.

^ We can even create smaller functions to let us communicate how these components interact

^ MVVM and view models really faciliate these interactions, as well as concepts like functional view controllers

---

### Immutability
### Source of Truth
## [fit] Value vs. Reference types
### Getting `func`y

---

# Value Types

^ Swift also gives us some really fantastic value types from _not only_ things like structs and enums, but gave us more reliable value type semantics around strings, numbers, arrays, dictionaries

^ Breathed new life into how we pass values around our app.

---

# [fit] Values

^ Value types, in juxtaposition to reference types, give us clear ownership of data.

---

```swift
struct Banana {
    var peeled: Bool = false
}

var banana = Banana(peeled: true) // {peeled true}
var bananaReference = banana      // {peeled true}
bananaReference.peeled = false    // {peeled false}
banana                            // {peeled true}
bananaReference                   // {peeled false}
```

^ In Swift, when we use value types, we don't need to worry about the data as we pass it around our system. As we pass values around, their semantics only let their values exist as is, or with specific mutating semantics.

^ They let us more easily reason about our code, without worrying if our bananas will get peeled right under our noses without us knowing.

---

# Values as data

^ One enormous benefit of this is that we can start to reason better not only about our code, but about the data inside of our code.

---

# Objects are not data

^ Objects have unexpected behaviors, shared behaviors, shared state.

---

# Data is data

^ Data is just data. Inert.

---

# Values are data

^ And values are inert.

---

> "Let data be data."
-- Rich Hickey

---

> "Let data be data."
-- Rich Hickey (again)

![](images/rich-hickey-2.jpg)
![](images/rich-hickey.jpg)

---

# Values Reduce State

^ I propose to you that using value types in your application will help you reduce state and provide a source of data that is reliable due to it's immutable properties.

---

### Pistachio[^1]

```swift

struct Origin {
  var city: String

  init(city: String = "") {
    self.city = city
  }
}

struct Person {
  var name: String
  var origin: Origin

  init(name: String = "", origin: Origin = Origin()) {
    self.name = name
    self.origin = origin
  }
}

```

^ Libraries like Pistachio (using Monocle) really showcase the value of value types like this, and how lenses can even give new light to data in a way that leaves data as data.

^ Let's take a look at their README even

^ Given a couple of simple models, a named city or Origin, and a person with a name and Origin

[^1]: from https://github.com/felixjendrusch/Pistachio && https://github.com/robb/Monocle

---

### Pistachio[^1]

```swift
struct OriginLenses {
  static let city = Lens(get: { $0.city }, set: { (inout origin: Origin, city) in
    origin.city = city
  })
}

struct PersonLenses {
  static let name = Lens(get: { $0.name }, set: { (inout person: Person, name) in
    person.name = name
  })

  static let origin = Lens(get: { $0.origin }, set: { (inout person: Person, origin) in
    person.origin = origin
  })
}

let felix = Person(name: "Felix", origin: Origin(city: "Berlin"))
let robb = set(PersonLenses.name, person, "Robb")
get(PersonLenses.name, robb) // == "Robb"
felix.name // == "Felix"

```

[^1]: from https://github.com/felixjendrusch/Pistachio && https://github.com/robb/Monocle

^ We can construct lenses which leave the data in tact, but give us the benefit of immutable, reliable value types

^ I won't go into the other benefits and disadvantages of lenses or use the word monad to lull you to sleep, but I wanted to show you a way that by using values, we can derive a reliable immutable source of truth in our application

---

### Immutability
### Source of Truth
### Value vs. Reference types
## Getting `func`y

^ At this point, we haven't drifted to far away from what is familiar or close to hand, in terms of iOS development.

^ Let's spend a bit of time diving into the unknown to see what we can things we can learn from other paradigms.

^ Let's do a bit of exploration into a few different programming paradigms and compare how they work with data

---

# Procedural

```swift
// Need a class here. With a struct:
// `immutable value of type '[Banana]' only has mutating members named 'append'`
class Monkey {
    var stomach: [Banana] = []
}
```

^ Take for example, this Monkey class. A monkey, who has a stomach who we can shovel bananas into.

---

# Procedural

```swift
// Need a class here. With a struct:
// `immutable value of type '[Banana]' only has mutating members named 'append'`
class Monkey {
    var stomach: [Banana] = []
}

let monkey = Monkey()
func eat() {
  monkey.stomach.append(Banana(peeled: true))
}
eat()
monkey.stomach    // == [{peeled: true}]
```

^ Procedurally, we might write something like this, where we define an `eat` function which adds food into the monkeys stomach.

^ We call `eat()` and we see that the monkey's stomach now has a banana inside

^ This has some inherent problems to it. The monkey's stomach is shown to us, we have a knowledge of the deep structure of what a monkey is and how it eats

^ Also, calling this eat() method is destructive, we lose the values of the monkey's stomach before and thus mutate the monkey itself, losing our benefit of value types

---

# Object-Oriented

```swift
class Monkey {
    private var stomach: [Banana] = []
    func eat(banana: Banana) {
        stomach.append(banana)
    }
}

let monkey = Monkey()
monkey.eat(Banana(peeled: true))
```

^ This helps us out a bit. We no longer have to expose the monkey's stomach to feed him

---

# Object-Oriented

```swift
class Monkey {
    var stomach: [Banana] = []
    func eat(banana: Banana) {
        stomach.append(banana)
    }
}

let monkey = Monkey()
monkey.eat(Banana(peeled: true))  // Happy 🐵
```

^ We hide the internals of the monkey, not having to know or wanting to know what the internals are

^ We've gotten things a bit simpler

---

- Procedural: Mutable + Separate Data & Code
- Object-Oriented: Mutable + Combined Data & Code

^ In the first example, we separated out the data (stomach) from the feeding
^ Combined Data & Code == Objects

---

# Functional

```swift
struct Monkey {
    let stomach: [Banana]
}

func eat(monkey: Monkey, banana: Banana) -> Monkey {
    let stomach = monkey.stomach + [banana]
    return Monkey(stomach: stomach) // New 🐒, new stomach
}
```

^ Build a new stomach for the monkey, build a new monkey

^ Fosters immutability

---

# Functional

```swift
struct Monkey {
    let stomach: [Banana]
}

func eat(monkey: Monkey, banana: Banana) -> Monkey {
    let stomach = monkey.stomach + [banana]
    return Monkey(stomach: stomach) // New 🐒, new stomach
}

let monkey = Monkey(stomach: [])
let fedMonkey = eat(monkey: monkey, banana: Banana(peeled: true))

monkey == fedMonkey // == false

monkey.stomach // == []
fedMonkey.stomach // == [{peeled: true}]

```


^ As our monkey's stomach changes, our monkey changes

^ Values over time, without the complexity of mutability

---

```swift
let troop = [
    Monkey(stomach: []),
    Monkey(stomach: []),
    Monkey(stomach: [])
]
```

^ Let's get a little bit more creative here.

^ Say we have a troop of monkeys to feed now

---

```swift
let troop = [
    Monkey(stomach: []),
    Monkey(stomach: []),
    Monkey(stomach: [])
]

let fedTroop = troop.map {
    eat($0, Banana(peeled: true))
}

troop         // == Array of unfed 🙊
fedTroop      // == Array of monkeys with one banana in their stomach
```

^ We can then feed the monkeys by mapping over the troop, applying the eat function, supplying a monkey and a banana, and our result is...

^ ...new fed monkeys

^ Notice again, we never mutated the monkeys. We've removed the complexity of time by having explicit values of monkeys for each state. We no longer need to worry about how a monkey has changed, only that it has and that our monkey is now fed and happy

---

```swift
let troop = [
    Monkey(stomach: []),
    Monkey(stomach: []),
    Monkey(stomach: [])
]

let fedTroop = troop.map {
    eat($0, Banana(peeled: true))
}

let bananaCount = fedTroop.map { return $0.stomach.count }.reduce(0, combine: +)
bananaCount // == 3

// `map` a second time
let superfedTroop = troop.map {
    eat(eat($0, Banana(peeled: true)), Banana(peeled: true))
}

let bananaRecount = fedTroop.map { return $0.stomach.count }.reduce(0, combine: +)
bananaRecount // == 3

let superfedCount = superfedTroop.map { return $0.stomach.count }.reduce(0, combine: +)
superfedCount // == 6

```

^ We can begin to do some really neat things like get the count of the bananas that our troop has eaten.

^ Notice that even after we map a second time, our `fedTroop`s state has still remained unchanged, even if our actions take place on the `troop` again

---

- Procedural: Mutable + Separate Data & Code
- Object-Oriented: Mutable + Combined Data & Code
- Functional: Immutable + Separate Data & Code

^ We can see some parallels to functional and procedural, but here we have no changes or mutations happening to our data, only new data

---

- Procedural: Mutable + Separate Data & Code
- Object-Oriented: Mutable + Combined Data & Code
- Functional: Immutable + Separate Data & Code
- FauxO: Immutable + Combined Data & Code

^ There's one more group here that I'd like to talk about, which Gary Bernhardt dubbed "FauxO".

^ The idea here is that in our functional example, we had our data separate from our `eat`ing code.

^ FauxO helps bring us back closer to our OO brother, while still adopting some functional mentality

---

# Functional

```swift
struct Monkey {
    let stomach: [Banana]
}

func eat(monkey: Monkey, banana: Banana) -> Monkey {
    let stomach = monkey.stomach + [banana]
    return Monkey(stomach: stomach) // New 🐒, new stomach
}
```

^ In our functional example we had something like this

---

# FauxO

```swift
struct Monkey {
    let stomach: [Banana]

    func eat(banana: Banana) -> Monkey {
        return Monkey(stomach: stomach + [banana]) // New 🐒, new stomach
    }
}
```

^ FauxO brings that `eat` function closer to our data, without mutating it.

^ We've regained some of the nice things we like about OO

^ This is still functional!

---

- Procedural: Mutable + Separate Data & Code
- Object-Oriented: Mutable + Combined Data & Code
- Functional: Immutable + Separate Data & Code
- FauxO: Immutable + Combined Data & Code

---

## Immutability
## Source of Truth
## Value vs. Reference types
## Getting `func`y

^ I hope I've given you some brainfood with all of these topics

^ I challenge you to try some new ways of thinking and reasoning about your code

^ And as always

---

![](images/taylor-swift-haters.gif)

^ Haters gonna hate and staters gonna state.

---

- Rich Hickey - "Simple Made Easy"
- Gary Bernhardt - "Boundaries"
- Lee Byron - "Immutable Data & React"
- Learn You as Haskell
- Justin Spahr-Summers - "Enemy of the State"
- Andy Matuschak - WWDC 2014: Session 229 [^2]

[^2]: ...or pretty much anything Andy says.

^ FOr more information, check out these talks

---

# Thanks

## @_eliperkins

^ What questions do you have for me?
