---
title: The Composite Reuse Principle - Composition over Inheritance
date: 2025-08-29 15:20:00 +0200
categories: [Design Principles]
tags: [principles, composition, inheritance]     # TAG names should always be lowercase
---
## Introduction
Ahoy and welcome to Swift for Pirates!

Today, we want to talk about the **Composite Reuse Principle** which states that when it comes to reusing code to avoid duplication it’s in most cases best to prefer **composition over inheritance**.

Let’s say we have three types of ships.
```swift
class PirateShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func shootCannon(_ direction: String) {
		print("Shooting cannon to the \(direction). Boom!")
	}

	func plunder() {
		print("Plundering")
	}
}

class MerchantShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func load(_ goods: String) {
		print("Loading \(goods)")
	}

	func unload(_ goods: String) {
		print("Unloading \(goods)")
	}
}

class PirateHunterShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func shootCannon(_ direction: String) {
		print("Shooting cannon to the \(direction). Boom!")
	}

	func arrestPirates() {
		print("Game over!")
	}
}
```

As you see, we have some duplication here. All three ships have the same `sail` method, and two of them have `shootCannon`. Let’s try to improve this by using class inheritance.

## Inheritance
So let’s define a superclass `Ship`.
```swift
class Ship {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}
}
```

Now all ships can inherit this method by subclassing Ship. So they don’t have to duplicate the `sail` method anymore.
```swift
class MerchantShip: Ship {
	func load(_ goods: String) {
		print("Loading \(goods)")
	}

	func unload(_ goods: String) {
		print("Unloading \(goods)"
	}
}

class PirateShip: Ship {
	func shootCannon(_ direction: String) {
		print("Shooting cannon to the (direction). Boom!")
	}

	func plunder() {
		print("Plundering")
	}
}

class PirateHunterShip: Ship {
	func shootCannon(_ direction: String) {
		print("Shooting cannon to the (direction). Boom!")
	}

	func arrestPirates() {
		print("Game over!")
	}
}
```

The method `shootCannon(:)` is still duplicated. So let’s create another subclass of Ship.
```swift
class ArmedShip: Ship {
	func shootCannon(_ direction: String) {
		print("Shooting cannon to the (direction). Boom!")
	}
}
```

Now all armed ships can inherit this method instead of duplicating it.
```swift
class PirateShip: ArmedShip {
	func plunder() {
		print("Plundering")
	}
}

class PirateHunterShip: ArmedShip {
	func arrestPirates() {
		print("Game over!")
	}
}
```

This works great so far. We’ve eliminated all duplication.

But now, with all those pirates haunting the seas these days, more and more merchants choose to arm their ships so they can defend themselves.

These are the methods we need for an armed merchant ship:
```swift
class ArmedMerchantShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func shootCannon(_ direction: String) 
		print("Shooting cannon to the (direction). Boom!")
	}

	func load(_ goods: String) {
		print("Loading \(goods)")
	}

	func unload(_ goods: String) {
		print("Unloading \(goods)"
	}
}
```

But now we have a lot of duplication again.

If we inherit from ArmedShip, we can get rid of the duplicated `sail` and `shootCannon` methods.
```swift
class ArmedMerchantShip: ArmedShip {
	func load(_ goods: String) {
		print("Loading \(goods)")
	}

	func unload(_ goods: String) {
		print("Unloading \(goods)"
	}
}
```

To avoid duplicating `load(:)` and `unload(:)`, we would have to inherit from `MerchantShip`. But in Swift, you can only inherit from a single class.

So that’s one limitation of inheritance. It’s not that flexible.

Inheritance is described as an **is-a** relationship. A `MerchantShip` **is a** `Ship`. A `PirateShip` **is an** `ArmedShip`. It’s all or nothing. If you decide to inherit from a class, not only do you inherit all properties and behavior of that class, but you **become** a kind of that class. That’s the strongest form of coupling.

## Composition
Now let’s try to solve the same problem with composition instead of inheritance. Let’s start with our three ships again. Remember we wanted to eliminate the duplication here.
```swift
class PirateShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func shootCannon(_ direction: String) {
		print("Shooting cannon to the \(direction). Boom!")
	}

	func plunder() {
		print("Plundering")
	}
}

class MerchantShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func load(_ goods: String) {
		print("Loading \(goods)")
	}

	func unload(_ goods: String) {
		print("Unloading \(goods)")
	}
}

class PirateHunterShip {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}

	func shootCannon(_ direction: String) {
		print("Shooting cannon to the \(direction). Boom!")
	}

	func arrestPirates() {
		print("Game over!")
	}
}
```

In order to work with composition, we need to think small. For example, who or what is it that can shoot, is it really the whole ship?
No, it’s only the cannon. So let’s define a tiny class `Cannon` that only does this one thing.
```swift
class Cannon {
	func shoot(_ direction: String) {
		print("Shooting cannon to the \(direction). Boom!")
	}
}
```

What do we need to sail? A mast, a sail, a helm, a rudder, but we need all of these together. Let’s call it `SailingEquipment`.
```swift
class SailingEquipment {
	func sail(_ direction: String) {
		print("Sailing \(direction)")
	}
}
```

Now, who does the plundering? Is it the ship itself? No, let’s say that’s the `BoardingCrew`.
```swift
class BoardingCrew {
	func plunder() {
		print("Plundering")
	}
}
```

What does a ship need to be able to load and unload goods? We need a `Hold`.
```swift
class Hold {
	func load(_ goods: String) {
		print("Loading \(goods)")
	}

	func unload(_ goods: String) {
		print("Unloading \(goods)"
	}
}
```

And finally, who is arresting pirates? Let’s call them the `NavalOfficers`.
```swift
class NavalOfficers {
	func arrestPirates() {
		print("Game over!")
	}
}
```

And now we have all these tiny components and can compose them to our liking.

A `PirateShip` should be able to sail, shoot, and plunder. So it needs `SailingEquipment`, a `Cannon`, and a `BoardingCrew`. Then we can use the cannon to shoot, the boarding crew to plunder, and so on.
```swift
class PirateShip {
	let sailingEquipment: SailingEquipment
	let cannon: Cannon
	let boardingCrew: BoardingCrew

	init(sailingEquipment: SailingEquipment, cannon: Cannon, boardingCrew: BoardingCrew) {
		self.sailingEquipment = sailingEquipment
		self.cannon = cannon
		self.boardingCrew = boardingCrew
	}

	func attack(_ direction: String) {
		sailingEquipment.sail(direction)
		cannon.shoot(direction)
		boadingCrew.plunder()
	}
}
```

Remember that I said that inheritance is called an **is-a** relationship? With composition, we have a **has-a** relationship. A `PirateShip` **has a** cannon it can use to shoot. It **has a** boardingCrew that can be sent out to plunder.

Next, let’s do the same with the `MerchantShip`. The `MerchantShip` now **has a** hold to load and unload goods.
```swift
class MerchantShip {
	let sailingEquipment: SailingEquipment
	let hold: Hold

	init(sailingEquipment: SailingEquipment, hold: Hold) {
		self.sailingEquipment = sailingEquipment
		self.hold = hold
	}

	func exchange(_ oldGoods: String, with newGoods: String) {
		hold.unload(oldGoods)
		hold.load(newGoods
	}
}
```

And now the `PirateHunterShip`.
```swift
class PirateHunterShip {
	let sailingEquipment: SailingEquipment
	let cannon: Cannon
	let navalOfficers: NavalOfficers

	init(sailingEquipment: SailingEquipment, cannon: Cannon, navalOfficers: NavalOfficers) {
		self.sailingEquipment = sailingEquipment
		self.cannon = cannon
		self.navalOfficers = navalOfficers
	}
}
```

You get the idea. But now let’s bring in our problem case again, the `ArmedMerchantShip`. Remember, this one needs to be able to sail, shoot, load, and unload.
```swift
class ArmedMerchantShip {
    func sail(_ direction: String) {
        print("Sailing \(direction)")
    }

    func shootCannon(_ direction: String) {
        print("Shooting cannon to the \(direction). Boom!")
    }

    func load(_ goods: String) {
        print("Loading \(goods)")
    }

    func unload(_ goods: String) {
        print("Unloading \(goods)")
    }
}
```

This seemed impossible to solve with inheritance. But with our composition approach, it’s easy. We just need sailing equipment, a cannon, and a hold.
```swift
class ArmedMerchantShip {
    let sailingEquipment: SailingEquipment
    let cannon: Cannon
    let hold: Hold

    init(sailingEquipment: SailingEquipment, cannon: Cannon, hold: Hold) {
        self.sailingEquipment = sailingEquipment
        self.cannon = cannon
        self.hold = hold
    }
}
```

So composition is much more flexible than inheritance.

And here comes the next step. You can make this even more composable by using protocols instead of concrete types. For example, let’s make `Cannon` a protocol instead of a concrete class.
```swift
protocol Cannon {
	func shoot(_ direction: String)
}
```

Now our ships can use different kinds of cannons.
```swift
class LongRangeCannon: Cannon {
	func shoot(_ direction: String) {
		print("Shooting cannon FAR to the \(direction). Boom!")
	}
}

class LoudCannon: Cannon {
	func shoot(_ direction: String) {
		print("Shooting cannon to the \(direction). BOOOOM!")
	}
}
```

Let’s create an armed merchant ship with an extra loud cannon to scare away the pirates.
```swift
let loudCannon = LoudCannon()
let loudMerchant = ArmedMerchantShip(sailingEquipment: SailingEquipment(), cannon: loudCannon, hold: Hold())
```

And here is an extra dangerous pirate hunter with a long range cannon.
```swift
let longRangeCannon = LongRangeCannon()
let dangerousHunter = PirateHunterShip(sailingEquipment: SailingEquipment(), cannon: longRangeCannon, navalOfficers: NavalOfficers())
```

And so we can compose our objects however we like and create components with different behavior even at runtime.

To sum up some of the advantages of composition: Using composition, our code becomes more flexible and easier to change. These tiny components are easier to develop and reason about than an inheritance hierarchy of classes. They are also easier to test, easier to reuse, and easier to maintain.

So in most cases, composition is better than class inheritance if you want to reuse or share code.

A better use of inheritance is extending the functionality of existing classes, like inheriting from UIViewController and overriding its lifecycle methods.

There is more to be said on this topic. If you’re interested, I’ll put some links below.
Fair winds and goodbye!

—-

Further learning and sources:
- I can't recommend enough the iOS Lead Essentials program at https://www.essentialdeveloper.com.
- Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides: Design Patterns - Elements of Reusable Object-Oriented Software, p. 18-20
- https://en.wikipedia.org/wiki/Composition_over_inheritance
