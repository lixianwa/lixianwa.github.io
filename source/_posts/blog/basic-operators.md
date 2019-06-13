---
title: "ReactiveCocoa中的基本算子"
date: 2016-08-14
categories: 技术
tags: ["算子", "ReactiveCocoa"]
---

原文链接: [Basic Operators](https://github.com/ReactiveCocoa/ReactiveCocoa/blob/master/Documentation/BasicOperators.md)

这个文档解释ReactiveCocoa中最常用的Operator，以及一些用例。注意Operators，在这里指的是signal和signal producers之间的转换，不是Swift中的操作符，换句话说，这些是ReactiveCocoa提供处理事件流的组合原语。该文档使用术语“事件流”来描述Signal和SignalProducer的概念。

### 事件流副作用
#### 观察（Observation）
Signal可以被观察通过observe函数，需要一个Observer作为参数，任何事件流都会发送给它。
```
signal.observe(Signal.Observer { event in
  switch event {
    case let .Next(next):
      print("Next: \(next)")
    case let .Failed(error):
      print("Failed: \(error)")
    case .Completed:
      print("Completed")
    case .Interrupted:
      print("Interrupted")
  }
})
```
<!--more--> 

或者可以用Next, Failed, Completed 和Interrupted事件的回调函数，当相应的事件发生时它（回调函数）会被调用。
```
signal.observeNext { next in
  print("Next: \(next)")
}
signal.observeFailed { error in
  print("Failed: \(error)")
}
signal.observeCompleted {
  print("Completed")
}
signal.observeInterrupted {
  print("Interrupted")
}
```
注意可以不用观察所有四种类型的事件，因为参数是可选类型, 你只需要给你关心的事件提供回调函数。

#### Injecting effects
SignalProducer会引入副作用，当使用on操作符但没有实际订阅它。

```
let producer = signalProducer
  .on(started: {
    print("Started")
  }, event: { event in
    print("Event: \(event)")
  }, failed: { error in
    print("Failed: \(error)")
  }, completed: {
    print("completed")
  }, interrupted: {
    print("interrupted")
  }, terminated: {
    print("Terminated")
  }, disposed: {
    print("Disposed")
  }, next: { value in
    print("Next: \(value)")
  })
```
与observe类似，你只需要给你关心的事件提供回调函数。
注意不会有任何打印直到producer调用started（也许再其他任何地方调用started）。

### 组合算子（Operator composition）
#### Lifting
转换事件流
以下操作符可以将一个事件流转换成一个新的事件流。

#### Mapping
map用于事件流中所有的值，然后将结果创建成一个新的流。
```
let (signal, observer) = Signal<String, NoError>.pipe()

signal
  .map{ string in string.uppercaseString }
  .observeNext { next in print(next) }

  observer.sendNext("a") //Prints A
  observer.sendNext("c") //Prints B
  observer.sendNext("c") //Prints C
```
#### Filtering
**filter** 用于包含满足一定条件的事件流的值。
```
let (signal, observer) = Signal<Int, NoError>.pipe()

signal
  .filter { number in number % 2 == 0 }
  .observeNext { next in print(next) }

observer.sendNext(1)   //Not printed
observer.sendNext(2)   //Prints 2
observer.sendNext(3)   //Not printed
observer.sendNext(4)   //Print 4
```
##### 聚集(Aggregating)
**reduce** 用于将一个事件流中的所有值聚集成一个单一的合并后得值，注意只有当输入的流完成时才会得到最后的结果。
```
let (signal, observer) = Signal<Int, NoError>.pipe()
signal
  .reduce{ $0 * $1 }
  .observeNext{ next in print(next) }

observer.sendNext(1) //Not printed
observer.sendNext(2) //Not printed
observer.sendNext(3) //Not printed
observer.sendNext(4) //Not printed
observer.sendCompleted() //print 10
```
**collect** 将一个事件流的所有值聚合到一个数组中，注意只有当输入的流完成时才会得到最后的结果。
```
let (signal, observer) = Signal<Int, NoError>.pipe()

signal
    .collect()
    .observeNext { next in print(next) }

observer.sendNext(1)     // nothing printed
observer.sendNext(2)     // nothing printed
observer.sendNext(3)     // nothing printed
observer.sendCompleted()   // prints [1, 2, 3]
```
### 合并事件流（Combining event streams）
这些操作会合并多个事件流中的值成一个新的、统一的流。

#### 合并最新的值（Combining latest values）
**combineLatest** 方法合并两个事件流最后的值。
产生的流将会发送它的第一个值当每一个输入流至少有一个值发送之后，之后，每一个输入流的新值产生一个新值输出。
```
let (numbersSignal, numbersObserver) = Signal<Int, NoError>.pipe()
let (lettersSignal, lettersObserver) = Signal<Int, NoError>.pipe()

let signal = combineLatest(numbersSignal, lettersSignal)
signal.observeNext { next in print(next) }
signal.observeCompleted { print("Completed") }

numbersObserver.sendNext(0)      // nothing printed
numbersObserver.sendNext(1)      // nothing printed
lettersObserver.sendNext("A")    // prints (1, A)
numbersObserver.sendNext(2)      // prints (2, A)
numbersObserver.sendCompleted()  // nothing printed
lettersObserver.sendNext("B")    // prints (2, B)
lettersObserver.sendNext("C")    // prints (2, C)
lettersObserver.sendCompleted()  // prints "Completed"
combineLatestWith算子功能相同，不同的是它是一个算子。
```
### 压缩（Zipping）
**zip**函数结合两个事件流成一对。任何第N个元组的元素相当于第N个输入流的元素。
意思是说输出流的第N个值只有当每个输入流发送了至少N个值之后才会发送。
```
let (numbersSignal, numbersObserver) = Signal<Int, NoError>.pipe()
let (lettersSignal, lettersObserver) = Signal<String, NoError>.pipe()

let signal = zip(numbersSignal, lettersSignal)
signal.observeNext { next in print("Next: \(next)") }
signal.observeCompleted { print("Completed") }

numbersObserver.sendNext(0)      // nothing printed
numbersObserver.sendNext(1)      // nothing printed
lettersObserver.sendNext("A")    // prints (0, A)
numbersObserver.sendNext(2)      // nothing printed
numbersObserver.sendCompleted()  // nothing printed
lettersObserver.sendNext("B")    // prints (1, B)
lettersObserver.sendNext("C")    // prints (2, C) & "Completed"
```
**zipWith**算子功能相同，不同之处是它是一个算子。