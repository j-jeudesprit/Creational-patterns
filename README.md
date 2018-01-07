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
  
Представьте, что у Вас есть семейство объектов, скажем кресло, диван и журнальный столик или даже один объект в семействе, которая в последствии может расшириться. Проблема расширения семейства решается достаточно просто, путём введения некоего интерфейса, который бы описывал объекты Вашего семейства(подумали Вы). Таким образом в коде Вы будете работать с интерфейсом, а не с конкретным типом. Но один интерфейс полностью не избавит от проблем, остаётся проблема создания самого объекта. Ведь мы бы не хотели лезть в код и добавлять инициализацию новых классов семейства в тех местах, где она необходима. Что бы решить уже эту проблему(проблему инстанцирования), мы воспользуемся такой практикой, которая называется **Фабрикой** и о которой поговорим чуть позже.     
  
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

***
[Builder](#builder)  
--------------      
![](https://refactoring.guru/images/patterns/content/builder/builder-2x.png)   
  
**Строитель** — это порождающий паттерн проектирования, который отделяет процесс конструирования сложного объекта от его представления.   
  
Представим, что нам нужно создать какой-либо сложный объект. Скажем, начиная с немного абстрактного примера, построения дома. Ну вот незадача, дома бывают очень разные. Они могут отличаться количеством этажей, комнат, ванн и т.д. Дома могут быть с бассейном, с газоном и т.д. Первое, что может прийти в голову, это создать некий класс представление -  *Дом*, а затем наследоваться от этого класса и реализовывать нужные подклассы. Но такой подход приводит к тому, что подклассов может стать очень много. Второй подход, это сделать достаточно конструируемым наш базовый класс представления - *Дом*. В таком случаи наш класс становится достаточно большим, а ведь мы только реализовали конструирования, но у представления могут быть и другие заботы. Эту проблему призван решить паттерн **Строитель**.  
    
Паттерн **Строитель** отделяет конструирование от представления и делегирует его создание другим классам, так называемых - строителей. Как это происходит? Для начала мы создаём интерфейс, который будет содержать все возможные методы, которые нужны для построения того или иного представления. То есть все возможные и необходимые для конфигурирования представления методы. Причём не обязательно реализовать их все. Давайте создадим такой:  
  
```swift  
@objc protocol Builder {
    @objc optional func buildStepA() // это может быть создание бассейна
    @objc optional func buildStepB() // газона
    @objc optional func buildStepC() 
	// ...
}
```
  
В нашем случаи все методы являются опциональными, хотя можно ограничиться лишь некоторыми из них. Как того требует ваша реализация. Далее нужно реализовать конкретных строителей. Тут нужно понимать, что конкретные строители, могу возвращать как объект одного типа, так и объекты других типов. Что это значит? Ну допустим какой-то из строителей возвращает дома с бассейном и газоном, но при этом другой строитель будет возвращать тоже самое(то есть тот же тип), только отличаться они будут лишь материалами из которых они сделаны, или ещё чем. А вот другие строители уже могут возвращать дома без бассейна, или скажем какие-то особые дома(что уже будет являться другим типом). Покажем на примере:  
    
```swift
class Builder1: Builder {
    private(set) var result = Product1()

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
    private(set) var result = Product1()

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

class Builder3: Builder {
    private(set) var result = Product2()

    func buildStepA() {
        // ...
    }

    func buildStepC() {
        // ...
    }
}
```

В принципе уже на этом этапе можно сказать, что мы реализовали паттерн. Создавая нужных нам строителей и вызывая нужные нам методы, мы сможем построить любой из домов. При этом основной класс-представитель *Дом* не захламлён конструированием самих домов.   
  
Но зачастую, создание домов лучше делегировать(предоставить) другому классу, именуемым *Директором*. Для чего он нужен? Возможно сам процесс построения дома достаточно сложен. Пусть у нас и есть разного рода строители, но мы можем легко ошибиться и не вызвать при строительстве какой-то метод(останемся без ванной, скажем) и хотелось бы, что бы был некий класс, который будет знать в каком порядке вызывать методы строителей. Это повторюсь можно и не делать, если логика построения у вас проста, хоть и строителей огромное множество. Как в нашем примере выше, где у нас всего-та три метода(при этом вы не должны думать, что раз у нас всего та три метода, то на кой чёрт нам нужны вообще эти строители?). Они нужны, потому-что вариации их реализаций может быть много(стены кирпичные, каменные и т.д.).    
  
Класс *Директор* обязан принимать объект строителя. При чём сам тип строителя, которые мы указываем в классе, должен быть типом интерфейса строителя, т.к. строителей у нас много и передать мы можем любой из них. Далее в этом классе мы реализуем любую логику, которая нам нужна для построения нашего дома используя конкретного строителя, который мы передали ему. Давай-те реализуем такой класс:  
  
```swift
enum Types {
    case small
    case big
}

class Director {
    private var builder: Builder

// Логика тут может быть совершенна разная.  
// Мы можем проверять, какой билдер мы получили.  
// Можем проверять какой-то параметр, который можно передать Директору.
// В целом Директор должен уметь строить абсолютно любой дом, который мы у него попросим.
// А как он это будет делать, волновать нас не должно.  

    func make(_ type: Types) {
	    if let builder = builder as? Builder1 {
	        switch type {
	        case .small:
	            builder.buildStepA()
	            builder.buildStepB()
	        case .big:
	            builder.buildStepA()
	            builder.buildStepB()
	            builder.buildStepC()
	        }
	    } else {   
				// ...  
		}
    }
    
    func anotherMake() { 
	    // ...   
    }  

    init(builder: Builder) {
        self.builder = builder
    }
}
```

Теперь в самом коде должно выглядеть всё прозрачно. Легко и просто строится любой нужный нам объект.   
  
```swift    
// Main

let builder = Builder1()
let director = Director(builder: builder)
director.make(.big)
builder.result
```
