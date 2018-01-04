# Creational-patterns

Порождающие паттерны проектирования
===================

Фабричный метод
-------------

**Фабричный метод** — это порождающий паттерн проектирования, который определяет общий интерфейс для создания объектов в суперклассе, позволяя подклассам изменять тип создаваемых объектов.

![enter image description here](https://refactoring.guru/images/patterns/content/factory-method/factory-method-2x.png)

*Простой пример:* 
```swift
protocol Transport {
    func deliver()
}

class Truck: Transport {
    func deliver() {
        // ...
    }
}

class Ship: Transport {
    func deliver() {
        // ...
    }
}

class Logistics {
    static func createTransport(_ type: Types) -> Transport {
        switch type {
        case .truck: return Truck()
        case .ship: return Ship()
        }
    }

    func planDelivery() {
        // ...
    }

    enum Types {
        case truck
        case ship
    }
}

// Main

var transport = [Transport]()
transport.append(Logistics.createTransport(.truck))
transport.append(Logistics.createTransport(.ship))
transport[0].deliver()
transport[1].deliver()
```

*Сложный пример*: 
```swift
protocol Transport {
    func deliver()
}

class Truck: Transport {
    func deliver() {
        // ...
    }
}

class Ship: Transport {
    func deliver() {
        // ...
    }
}


protocol Logistics {
    func createTransport() -> Transport
}

extension Logistics {
    func planDelivery() {
        // ...
    }
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

// Main
var logistics = [Logistics]()
logistics.append(RoadLogistics())
logistics.append(SeaLogistics())

var transports = [Transport]()
for logistic in logistics {
    transports.append(logistic.createTransport())
    logistic.planDelivery()
}

```
