![](https://i.imgur.com/lrcE00f.png)  

***    
**Ссылки на конкретный паттерн**:  

 * [Abstract Factory](#abstract-factory)
 * [Builder](#builder)  
 * [Factory Method](#factory-method)
 * [Prototype](#prototype)
 * [Singleton](#singleton)      

***
[Abstract Factory](#abstract-factory)  
--------------      
![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-2x.png)   
  
**Абстрактная фабрика** — это порождающий паттерн проектирования, который предоставляет интерфейс для создания семейства взаимосвязанных объектов.   
  
Представьте, что у Вас есть семейство объектов, скажем  
{кресло, диван и журнальный столик} или даже один объект в семействе, которая в последствии может расшириться. Проблема расширения семейства решается достаточно просто, путём введения некоего интерфейса, который бы описывал объекты Вашего семейства(подумали Вы). Таким образом в коде Вы будете работать с интерфейсом, а не с конкретным типом. Но один интерфейс полностью не избавит от проблем, остаётся проблема создания самого объекта. Ведь мы бы не хотели лезть в код и добавлять инициализацию новых классов семейства в тех местах, где она необходима. Что бы решить уже эту проблему(проблему инстанцирования), мы воспользуемся такой практикой, которая называется **Фабрикой** и о которой поговорим чуть позже.     
  
Создадим указанные протоколы для каждого объекта нашего семейства:
  
```swift  
protocol Chair {
    func hasLegs()
    func sitOn()
}

protocol Sofa {
    func hasLegs()
    func sleepOn()
}  
  
//...
```
  
Теперь давайте подумаем о реализации этих протоколов. Возьмём протокол <i>Chair</i> и подумаем, какие могут быть реализации этого протокола? Кресло может быть сделано из различных материалов, иметь разную форму и т.п. Для наших примеров мы рассмотрим стили в которых можно делать мебель. Это будут ар-деко, модерн и викторианский стили. Эти стили будут применяться ко всему семейству, ведь мы изначально работаем с семейством объектов. 

Реализуем их:  
  
```swift
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

// ...
```

В нашем коде мы работаем исключительно с интерфейсами, что позволит нам с лёгкость добавлять различные реализации соответствующих протоколов(добавлять новые стили) и так же просто добавлять новые объекты в семейство(добавить скажем, шкаф).   
  
Но основная проблема теперь не в добавлении объектов и их реализаций, а в том, как нам правильно создавать семейства объектов одного стиля(ведь никто не хочет получить диван и кресло в викторианском стиле, а журнальный столик в стиле ар-деко). Создавать так, что бы в коде ничего не пришлось менять. Вот тут та к нам на помощь и приходит паттерн **Абстрактная фабрика**.   
  
**Абстрактная фабрика** - паттерн позволяющий решить проблему инстанцирования семейства объектов в нужной реализации. Для этого создадим интерфейс абстрактной фабрики: 

```swift
protocol FurnitureFactory {
    func createChair() -> Chair
    func createSofa() -> Sofa
}
```
  
Этот интерфейс определяет методы, которые создают семейство наших объектов. Т.к у нас всё построено на  интерфейсах, то и возвращаем мы не конкретную реализацию, скажем *VictorianChair*, а *Chair*. Теперь создадим соответствующие реализации данного интерфейса. Т.к. стилей у нас ровно три, то и таких реализации будет тоже три. Посмотрим на них:  

```swift
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
```

Теперь мы имеем три фабрики, каждая из которых создаёт семейство объектов в нужном стиле.  Осталось показать, как с этим работать в самом коде:

```swift  
class Client {
    private var chair: Chair
    private var sofa: Sofa
    //...    

    init(factory: FurnitureFactory) {
        chair = factory.createChair()
        sofa = factory.createSofa()
    }
}  

// Main

let victorianFactory = VictorianFactory()
let modernFactory = ModernFactory()
let artDekoFactory = ArtDecoFactory()

let client1 = Client(factory: victorianFactory)
let client2 = Client(factory: modernFactory)
let client3 = Client(factory: artDekoFactory)
```

Как видно, всё выглядит прозрачно. Не важно, какой стиль мы передаём и какие у нас объекты в системе. Для всех случаев нам не придётся изменять код, который работает с нашим семейством. Ведь вместо самих объектов используются интерфейсы, а вместо конкретной фабрики - абстрактная.  
