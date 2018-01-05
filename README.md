# Creational-patterns on Swift

Порождающие паттерны проектирования
===================

Фабричный метод 
-------------
Factory Method
-------------

**Фабричный метод** — это порождающий паттерн проектирования, который определяет общий интерфейс для создания объектов в суперклассе, позволяя подклассам изменять тип создаваемых объектов.

![enter image description here](https://refactoring.guru/images/patterns/content/factory-method/factory-method-2x.png)

*Классический пример:* 
```swift
// #####################

protocol Transport {
    func getType()
}

class Truck: Transport {
    func getType() {
        print("Truck")
    }
}

class Ship: Transport {
    func getType() {
        print("Ship")
    }
}

// #####################

protocol Logistics {
    // Фабричный метод
    func createTransport() -> Transport

    // Остальная функциональщина ...
}

class RoadLogistics: Logistics {
    func createTransport() -> Transport {
        return Truck()
    }
}

class SeaLogistics: Logistics {
    func createTransport() -> Transport {
        return Ship()
    }
}

// #####################
// Main

var logistics: [Logistics] = [
    RoadLogistics(),
    SeaLogistics(),
]

var transports = [Transport]()
for logistic in logistics {
    transports.append(logistic.createTransport())
    transports.last?.getType() // Truck, Ship
}
```

*Параметризованные фабричные методы*: 
```swift
// #####################

protocol Transport {
    func getType()
}

class Truck: Transport {
    func getType() {
        print("Truck")
    }
}

class Ship: Transport {
    func getType() {
        print("Ship")
    }
}

enum Types {
    case truck
    case ship
}

// #####################

protocol Logistics {
    // Параметризированный фабричный метод
    func createTransport(_ type: Types) -> Transport

    // Остальная функциональщина ...
}

class TransportLogistics: Logistics {
    func createTransport(_ type: Types) -> Transport {
        switch type {
        case .truck: return Truck()
        case .ship: return Ship()
        }
    }
}


// #####################
// Main

var logistics: [Logistics] = [
    TransportLogistics(),
    TransportLogistics(),
]

var transports = [Transport]()
transports.append(logistics[0].createTransport(.truck))
transports.append(logistics[1].createTransport(.ship))
transports[0].getType() // Truck
transports[1].getType() // Ship
```

*Использование шаблонов, чтобы не порождать подклассы*:
```swift
// #####################

protocol Transport {
    func getType()
}

// #####################
class Truck: Transport {
    func getType() {
        print("Truck")
    }
}

class Ship: Transport {
    func getType() {
        print("Ship")
    }
}

// #####################

protocol Logistics {
    // фабричный метод
    func createTransport() -> Transport?

    // Остальная функциональщина ...
}

// #####################

func ~= (lhs: Transport.Type, rhs: Transport.Type) -> Bool {
    return lhs == rhs ? true : false
}

// #####################

class TransportLogistics<Item: Transport>: Logistics {
    func createTransport() -> Transport? {
        switch Item.self {
        case Ship.self: return Ship()
        case Truck.self: return Truck()
        default: return nil
        }
    }
}

// #####################
// Main

var logistics: [Logistics] = [
    TransportLogistics<Truck>(),
    TransportLogistics<Ship>(),
]

var transports = [Transport]()
for logistic in logistics {
    transports.append(logistic.createTransport()!)
    transports.last?.getType() // Truck, Ship
}
```

*Клиенты тоже могут применять фабричные методы в обход создания отдельных фабрик, особенно при наличии параллельных иерархий классов*:
```swift
// #####################

protocol Manipulator {
    func move()
    func drag()
    func click()

    func getType()
}

protocol Figure {
    // Фабричный метод
    func createManipulator() -> Manipulator

     // Остальная функциональщина ...
}

// #####################

class CircleManipulator: Manipulator {
    func move() {
        // ...
    }

    func drag() {
        // ...
    }

    func click() {
        // ...
    }

    func getType() {
        print("Circle Manipulator")
    }
}

class Circle: Figure {
    func createManipulator() -> Manipulator {
        return CircleManipulator()
    }

    // Остальная функциональщина ...
}

class RectangleManipulator: Manipulator {
    func move() {
        // ...
    }

    func drag() {
        // ...
    }

    func click() {
        // ...
    }

    func getType() {
        print("Rectangle Manipulator")
    }
}

class Rectangle: Figure {
    func createManipulator() -> Manipulator {
        return RectangleManipulator()
    }

    // Остальная функциональщина ...
}

// #####################
// Main

var figures: [Figure] = [
    Circle(),
    Rectangle(),
]

var manipulators = [Manipulator]()
for figure in figures {
    manipulators.append(figure.createManipulator())
    manipulators.last?.click()
    manipulators.last?.drag()
    manipulators.last?.getType() // Circle Manipulator, Rectangle Manipulator
}
```

Абстрактная фабрика
-------------
Abstract Factory
-------------

**Абстрактная фабрика** — это порождающий паттерн проектирования, который позволяет создавать семейства связанных объектов, не привязываясь к конкретным классам создаваемых объектов.

![enter image description here](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-2x.png)

*Пример:*
```swift
// #####################

protocol Chair {
    func hasLegs()
    func sitOn()
}

class VictorianChair: Chair {
    func hasLegs() {
        // ...
    }

    func sitOn() {
        // ...
    }
}

class ModernChair: Chair {
    func hasLegs() {
        // ...
    }

    func sitOn() {
        // ...
    }
}

class ArtDecoChair: Chair {
    func hasLegs() {
        // ...
    }

    func sitOn() {
        // ...
    }
}

// #####################

protocol Sofa {
    func hasLegs()
    func sleepOn()
}

class VictorianSofa: Sofa {
    func hasLegs() {
        // ...
    }

    func sleepOn() {
        // ...
    }
}

class ModernSofa: Sofa {
    func hasLegs() {
        // ...
    }

    func sleepOn() {
        // ...
    }
}

class ArtDecoSofa: Sofa {
    func hasLegs() {
        // ...
    }

    func sleepOn() {
        // ...
    }
}

// #####################

protocol FurnitureFactory {
    func createChair() -> Chair
    func createSofa() -> Sofa
}

class VictorianFactory: FurnitureFactory {
    func createChair() -> Chair {
        return VictorianChair()
    }

    func createSofa() -> Sofa {
        return VictorianSofa()
    }
}

class ModernFactory: FurnitureFactory {
    func createChair() -> Chair {
        return ModernChair()
    }

    func createSofa() -> Sofa {
        return ModernSofa()
    }
}

class ArtDecoFactory: FurnitureFactory {
    func createChair() -> Chair {
        return ArtDecoChair()
    }

    func createSofa() -> Sofa {
        return ArtDecoSofa()
    }
}

// #####################

class Client {
    private var chair: Chair
    private var sofa: Sofa

    init(factory: FurnitureFactory) {
        chair = factory.createChair()
        sofa = factory.createSofa()
    }
}

// #####################
// Main

let victorianFactory = VictorianFactory()
let modernFactory = ModernFactory()
let artDekoFactory = ArtDecoFactory()

let client1 = Client(factory: victorianFactory)
let client2 = Client(factory: modernFactory)
let client3 = Client(factory: artDekoFactory)
```

Строитель 
-------------
Builder
-------------

**Строитель** — это порождающий паттерн проектирования, который позволяет создавать сложные объекты пошагово.

![enter image description here](https://refactoring.guru/images/patterns/content/builder/builder-2x.png)

*Классический пример:* 
```swift
// #####################

class Product1 {
    // ...
}

class Product2 {
    // ...
}

// #####################

protocol Builder {
    func reset()
    func buildStepA()
    func buildStepB()
    func buildStepC()
}

class Builder1: Builder {
    private(set) var result = Product1()

    func reset() {
        // ...
    }

    func buildStepA() {
        // ...
    }

    func buildStepB() {
        // ...
    }

    func buildStepC() {
        // ...
    }
}

class Builder2: Builder {
    private(set) var result = Product2()

    func reset() {
        // ...
    }

    func buildStepA() {
        // ...
    }

    func buildStepB() {
        // ...
    }

    func buildStepC() {
        // ...
    }
}

// #####################

enum Types {
    case small
    case big
}

class Director {
    private var builder: Builder

    func make(_ type: Types) {
        switch type {
        case .small:
            builder.buildStepA()
            builder.buildStepB()
        case .big:
            builder.buildStepA()
            builder.buildStepB()
            builder.buildStepC()
        }
    }

    init(builder: Builder) {
        self.builder = builder
    }
}

// #####################
// Main

let builder = Builder1()
let director = Director(builder: builder)
director.make(.big)
builder.result // big Product1
```

Прототип
-------------
Prototype
-------------

**Прототип** — это порождающий паттерн проектирования, который позволяет копировать объекты, не вдаваясь в подробности их реализации.

![enter image description here](https://refactoring.guru/images/patterns/content/prototype/prototype-2x.png)

*Пример:* 
```swift
// #####################

protocol Clonable {
    func clone() -> Self
}

// #####################

class Prototype: Clonable {
    var smth1: String = "Hello, "

    init() { }

    required init(prototype: Prototype) {
        smth1 = prototype.smth1
    }

    func clone() -> Self {
        return type(of: self).init(prototype: self)
    }

    // ...
}

class SubPrototype: Prototype {
    var smth2: String = "World!"

    override init() {
        super.init()
    }

    required init(prototype: Prototype) {
        fatalError("\(#function) has not been implemented")
    }

    required init(subprototype: SubPrototype) {
        smth2 = subprototype.smth2
        super.init(prototype: subprototype)
    }

    override func clone() -> Self {
        return type(of: self).init(subprototype: self)
    }

    // ...
}

// #####################
// Main

let proto = Prototype()
let clone1 = proto.clone()
clone1.smth1 // Hello,

let subproto = SubPrototype()
let clone2 = subproto.clone()
clone2.smth1 // Hello,
clone2.smth2 // World!
```

Одиночка
-------------
Singleton
-------------

**Одиночка** — это порождающий паттерн проектирования, который гарантирует, что у класса есть только один экземпляр, и предоставляет к нему глобальную точку доступа.

![enter image description here](https://refactoring.guru/images/patterns/content/singleton/singleton-2x.png)

*Пример:*
```swift
// #####################

class Singleton {
    static let shared = Singleton()

    private init() { }
}
```
