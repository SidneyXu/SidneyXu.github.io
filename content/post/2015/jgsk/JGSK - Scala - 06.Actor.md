---
comments: true
date: 2015-12-18T00:29:31+08:00
description: ""
draft: true
keywords:
- scala
slug: "jgsk-scala-06-actor"
tags:
- scala
title: JGSK - Scala - 06.Actor
toc: true
topics:
- JGSK
---


## Actor

### 概念

Actor 是 Scala 的并发模型。在 2.10 之后的版本中，Scala 抛弃了自身的 Actor 而是使用了 `Akka` 作为其推荐的 `Actor` 实现。

### 使用 Actor 

定义一个 Actor 只需要继承 Actor 特质并实现其中的 `receive()` 方法即可。除此之外，也可以实现其它的 Actor 的钩子方法 `preStart()`, `postStop()` 等。

创建一个 Actor

以下创建了一个回显的 Actor，其通过 `receive()` 接收到消息并且打印在控制台上。

```scala
class EchoServer extends Actor {
  def receive = {
    case msg: String => println("echo " + msg)
  }

  @throws[Exception](classOf[Exception])
  override def preStart(): Unit = {
    super.preStart()
    println("preStart")
  }
}
```

Actor 实例可以通过方法 `system.actorOf` 来进行创建。`ActorSystem` 可以用于创建多个基于同样配置的 Actor，也可以对 Actor 进行管理。

```scala
val system = ActorSystem()
val echoServer = system.actorOf(Props[EchoServer])
```

创建完 Actor 后可以通过 `!` 向 Actor 内部的邮箱发消息。

```scala
echoServer ! "hi"
```

控制台输出

```
preStart
echo hi
```

通过隐式转换，Scala 可以使用 DSL 语句将以上创建 Actor 的语句进行进一步简化

```scala
implicit val system = ActorSystem()
val echoServer = actor(new Act {
  become {
    case msg => println("echo " + msg)
  }
  whenStarting {
    println("preStart")
  }
})
echoServer ! "hi"
```


### Actor 和线程

- Scala 中 Actor 和线程是不同的抽象，他们的对应关系是由 Dispatcher 决定的。
- Actor 比线程轻量。在 Scala 中可以创建数以百万级的 Actor。奥秘在于 Actor 直接可以复用线程。
- Actor 和线程之间没有一对一的对应关系。一个 Actor 可以使用多个线程，一个线程也会被多个 Actor 复用。


### 配置 Dispatcher

每一个 ActorSystem 都有一个默认的 Dispatcher，但是也可以通过配置文件进行更改使用。

在工程的资源目录下创建 `application.conf` 文件。内容如下：

```json
my-dispatcher {
  # Dispatcher 的类型
  type = Dispatcher
  # ExecutionService 的类型
  executor = "fork-join-executor"
  # 配置 fork join 池
  fork-join-executor {
    # 最小线程数
    parallelism-min = 2
    # 线程增长因子
    parallelism-factor = 2.0
    # 最大线程数
    parallelism-max = 10
  }
  # 线程切换到另一个actor之前处理的消息数上限，1 为尽可能公平
  throughput = 100
}
```

以上就定义了一个名为 `my-dispatcher` 的 Dispatcher。

Dispatcher 共有四种类型：

- Dispatcher 默认类型，底层为 `java.util.concurrent.ExecutorService`，为每个 Actor 创建一个邮箱
- PinnedDispatcher 底层为 `akka.dispatch.ThreadPoolExecutorConfigurator`，为每个 Actor 创建一个邮箱
- BalancingDispatcher 底层为 `java.util.concurrent.ExecutorService`，为所有 Actor 创建一个邮箱，只有同一类型的 Actor 可以进行共享
- CallingThreadDispatcher 仅供测试

使用自定义的 Dispatcher

```scala
val echoServers = (1 to 10).map(x =>
  system.actorOf(Props(new EchoServer2(x.toString))
    .withDispatcher("my-dispatcher")))

(1 to 10).foreach(echoServers(Random.nextInt(10)) ! _)
```

以上例子中创建了 10 个 基于 `my-dispatcher` 的 Actor，每个 Actor 接收一个 0 到 10 的随机数作为消息。

除此之外，也可以通过 `lookup()` 方法获得已定义好的 Dispatcher 的实例

```scala
implicit val executionContext = system.dispatchers.lookup("my-dispatcher")
``` 

### Actor 间通信

Actor 间使用样本类可以发送更丰富的消息内容也能够轻易完成 Actor 间的通信。

定义作为消息的样本类，最后一个参数为发送消息的 Actor 的引用

```scala
case class Message[T <: ActorRef](content: String, sender: T)
```

使用该 Actor

```scala
implicit val system = ActorSystem()
val pingActor = actor(new Act {
  become {
    case Message(msg: String, sender: ActorRef) =>
      println(s"$msg pang")
  }
})
pingActor ! Message("ping", pingActor)
```


### 获得处理结果

Actor 发送消息后可以获得处理该消息的 Actor 的处理结果。要实现这功能，只需要使用 `ask()` 代替 `!` 发送消息就可以获得用于获得结果的 `Future` 对象。

```scala
import akka.actor.ActorDSL._
import akka.pattern.ask

implicit val ec = scala.concurrent.ExecutionContext.Implicits.global
implicit val system = akka.actor.ActorSystem()

val versionUrl = "https://github.com/SidneyXu"

val fromURL = actor(new Act {
  become {
    case url: String => sender ! scala.io.Source.fromURL(url)
      .getLines().mkString("\n")
  }
})

val versionFuture = fromURL.ask(versionUrl)(akka.util.Timeout(5, TimeUnit.SECONDS))
```

这个例子通过调用 `ask` 函数来获取一个 `Future`。`ask` 内部也是用 `!` 来传递消息，但是其可以同时设置超时时间。

通过调用 `Future` 的不同方法可以实现同步和异步操作：

获得同步结果

```scala
versionFuture.foreach(println)
```

获得异步结果

```scala
versionFuture onComplete {
  case msg => println(msg)
}
```


### 远程 Actor

Actor 可以进行远程调用，实现 RMI 的功能。

**服务端**

代码

```scala
import akka.actor.{ActorSystem, Props}

object RemoteServer extends App {

  implicit val system = ActorSystem("RemoteSystem")
  val remoteActor = system.actorOf(Props[EchoServer], name = "remoteServer")
  remoteActor ! "The RemoteActor is alive"

}
```

配置文件

配置文件需要放在 `classpath` 下， 系统默认读取的配置文件名为 `application.conf`

application.conf

```json
akka {
  actor {
    provider = "akka.remote.RemoteActorRefProvider"
  }
  remote {
    transport = "akka.remote.netty.NettyRemoteTransport"
    netty.tcp {
      hostname = "127.0.0.1"
      port = 5150
    }
  }
}
```

**客户端**

代码

```scala
object Client extends App {

  implicit val system = ActorSystem("LocalSystem", ConfigFactory.load("client"))
  val remote = system.actorSelection("akka.tcp://RemoteSystem@127.0.0.1:5150/user/remoteServer")
  remote ! "Hello from the LocalActor"

}
```

配置文件

可以通过 `ConfigFactory.load()` 来读取指定的配置文件，文件名不包含后缀名。

只有服务器端需要知道端口号，所以客户端的端口号设为 `0`。

client.conf

```json
akka {
  actor {
    provider = "akka.remote.RemoteActorRefProvider"
  }
  remote {
    transport = "akka.remote.netty.NettyRemoteTransport"
    netty.tcp {
      hostname = "127.0.0.1"
      port = 0
    }
  }
}
```

**运行**

先运行服务器端，控制台输出 "echo The RemoteActor is alive"，
再运行客户端，服务器端控制台会接着输出 "echo Hello from the LocalActor"






