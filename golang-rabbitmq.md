# Golang + RabbitMQ

## Sources

1. [RabbitMQ - Hello world](https://www.rabbitmq.com/tutorials/tutorial-one-go)

## Prerequisites

Make sure that you have a `RabbitMQ` instance running on your machine, For running **RabbitMQ** using `docker`, you can run this command:

```sh
docker run --name rabbitmq -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest -d --restart always rabbitmq:3-management
```

> [!NOTE]
> The `rabbitmq:3-management` will give you access to the UI management panel, but other tags like `latest`, may not include this UI panel.

## What is RabbitMQ?

RabbitMQ is a message broker: it accepts and forwards messages. You can think about it as a post office: when you put the mail that you want posting in a post box, you can be sure that the letter carrier will eventually deliver the mail to your recipient. In this analogy, RabbitMQ is a post box, a post office, and a letter carrier.

- **producing**

Producing is nothing more than sending data.
A program that sends a message is a `producer`.

- **queue**

A *queue* is the name for the postbox in RabbitMQ. Although messages flow through RabbitMQ and your application, they can only be stored inside a **queue**.
A queue is only bond with host's memory and disk limit.

Many Producers can send messages to one queue and many Consumers can try to receive data from one queue.

- **Consuming**

**Consuming** has a similar meaning to receiving. A consumer is a program that mostly waits to receive messages.

> [!IMPORTANT]
> Note that the **producer**, **consumer** and **broker** do not have to reside on the same host; indeed in most applications they don't!
> An application could be both a **producer** and **consumer**, too.

> RabbitMQ speaks in multi protocols, but I will focus on AQMP protocol now

### Install dependencies

For connecting to RabbitMQ and using `amqp` protocol to for speaking, We have to install a library in golang, which the official tutorial suggest this library:

```sh
go get github.com/rabbitmq/amqp091-go
```

### Sending

We will have a `send.go` file, that play the publisher role for us in this document.
The publisher will connect to RabbitMQ, send a single message then exit.

- **send.go**

```go
package main

import(
    "log"
    amqp "github.com/rabbitmq/amqp091-go"
)

const rabbitURL = "amqp://guest:guest@localhost:5672/"

func main(){
    conn, err := amqp.Dial(rabbitURL)
    if err != nil{
        log.Fatalf("failed to connect to rabbitmq: %v", err)
    }
    defer conn.Close()
}
```

The connection will abstract the socket connection for us and it handles authentication. Next we will declare a `channel`, Which has the most API functionalities, for working with rabbitmq:

```go
package main

import(
    "log"
    amqp "github.com/rabbitmq/amqp091-go"
)

const rabbitURL = "amqp://guest:guest@localhost:5672/"

func main(){
    conn, err := amqp.Dial(rabbitURL)
    if err != nil {
        log.Fatalf("failed to connect to rabbitmq: %v", err)
    }
    defer conn.Close()

    ch, err := conn.Channel()
    if err != nil {
        log.Fatalf("failed to create a channel: %v", err)
    }
    defer ch.Close()
}
```

For sending a data, we have to declare a `queue` for send data into, then we can publish a message to our queue:

```go
package main

import(
    "log"
    "time"
    "context"
    amqp "github.com/rabbitmq/amqp091-go"
)

const rabbitURL = "amqp://guest:guest@localhost:5672/"

func main(){
    conn, err := amqp.Dial(rabbitURL)
    if err != nil {
        log.Fatalf("failed to connect to rabbitmq: %v", err)
    }
    defer conn.Close()

    ch, err := conn.Channel()
    if err != nil {
        log.Fatalf("failed to create a channel: %v", err)
    }
    defer ch.Close()

    q, err := ch.QueueDeclare(
        "hello", // name
        false,   // durable
        false,   // delete when unused
        false,   // exclusive
        false,   // no-wait
        nil,     // arguments
    )
    if err != nil {
        log.Fatalf("failed to declare a queue: %v", err)
    }

    // Publish a Hello world message
    message := "Hello World!"
    ctx, cancel := context.WithTimeout(context.Background(), 5 * time.Second)

    err = ch.PublishWithContext(
        ctx,
        "", // exchange
        q.Name, // queue name
        false, // mandatory
        false, // immediate
        amqp.Publishing {
            ContentType: "text/plain",
            Body: message
        },
    )
    if err != nil {
        log.Fatalf("failed to publish a message: %v to queue: %v", message, q.name)
    }
    log.Printf(" [x] Sent %s\n", message)
}
```

declare a queue happens when there is no existing queue with given queue name, if exists, it will be used by client instead.
The message content is a `byte array`, so you can encode whenever you like.

### Receiving

That was our publisher, so now let's create a consumer for listen on RabbitMQ queue, unlike publisher that only send a message and then terminate the process, we will keep the consumer running for every new message on queue.

- **receive.go**

Setting up the connection is like what we did for our consumer, we first open up a new connection, then create a channel and declare a queue, and listen on queue for new messages. Thats all we need to do!

```go
package main

import(
    "log"
    amqp "github.com/rabbitmq/amqp091-go"
)

const rabbitURL = "amqp://guest:guest@localhost:5672/"

func main(){
    conn, err := amqp.Dial(rabbitURL)
    if err != nil {
        log.Fatalf("failed to create connection to RabbitMQ: %v", err)
    }
    defer conn.Close()

    ch, err := conn.Channel()
    if err != nil {
        log.Fatalf("failed to create a channel: %v", err)
    }
    defer ch.Close()

    q, err := ch.QueueDeclare(
        "hello", // queue name
        false,   // durable
        false,   // delete when unused
        false,   // exclusive
        false,   // no-wait
        nil,     // argument
    )
    if err != nil {
        log.Fatalf("failed to declare queue: %v", err)
    }

}
```

The above code, will just declare a queue and it will not listen on it until we tell RabbitMQ somehow.
We're going to tell the server to deliver new message on specified queue for us, and since it will push messages to us asynchronously, we'll read messages from a channel returned by `amqp::Consume` in a goroutine.

```go
package main

import(
    "log"
    amqp "github.com/rabbitmq/amqp091-go"
)

const rabbitURL = "amqp://guest:guest@localhost:5672/"

func main(){
    conn, err := amqp.Dial(rabbitURL)
    if err != nil {
        log.Fatalf("failed to create connection to RabbitMQ: %v", err)
    }
    defer conn.Close()

    ch, err := conn.Channel()
    if err != nil {
        log.Fatalf("failed to create a channel: %v", err)
    }
    defer ch.Close()

    q, err := ch.QueueDeclare(
        "hello", // queue name
        false,   // durable
        false,   // delete when unused
        false,   // exclusive
        false,   // no-wait
        nil,     // argument
    )
    if err != nil {
        log.Fatalf("failed to declare queue: %v", err)
    }
    messages, err := ch.Consume(
        q.Name, // queue name
        "",     // consumer
        true,   // auto-ack
        false,  // exclusive
        false,  // no-local
        false,  // no-wait
        nil,    // args
    )
    if err != nil {
        log.Fatalf("failed to consume on queue: %v", err)
    }
    var forever chan struct{}

    go func(){
        for d := range messages {
            log.Printf("Received a message: %s", d.Body)
        }
    }
    log.Printf("[*] Waiting for messages. To exit press CTRL+C")
    <-forever
}
```

Now we have our consumer that will listen on queue for new messages, and I will print it for us.
You can run each them using

```sh
go run send.go
```

```sh
go run receive.go
```
