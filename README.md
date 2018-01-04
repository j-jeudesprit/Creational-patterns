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
