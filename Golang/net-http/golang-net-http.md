# Golang net/http for building a rest server

## Source

-[Digital Ocean - How to make an HTTP Server in Go](https://www.digitalocean.com/community/tutorials/how-to-make-an-http-server-in-go)

The `net/http` package in go provides functionality for sending request or even creating a HTTP server, and it has functionalities for working with HTTP using go, while `net` package provides some other functionalities for network communication in go.

## Listening for requests and serving responses

At the first, we want to use `http.HandlerFunc` for defining our routes with their handler functions. For doing this, we just create a handler func that will recieve 2 parameters: `http.ResponseWriter`,  `*http.Request`, that will be filled by our http server and then pass it to the handlers. The HTTP server will fill these values for every incoming requests.

You can use `http.HandlerFunc` for define and bind a route to its handler.

In an `http.HandlerFunc`, the `http.ResponseWriter` value is used to control the response that you will return to incoming request, and it will use for filling the body of the response or its status code.

Also the `*http.Request` value is used to get the request information that incoming into the server, such as body in `POST` request or client headers and some other informations.

The `http.ResponseWriter` is an `io.Writer`, which means you can use any thing that implement that interface for write the response body. 

> [!NOTE]
> `io.Writer` is an interface that has a Write method for write bytes into an object.

```go
type Writer interface {
    Write([]byte)(int, error)
}
```

After creating our handlers, then we bind routes with handlers using `http.HandlerFunc`.
For creating a rest server, now we can use `http.ListenAndServe` for creating a rest server and start it.

### Example

This would be our simple server example:

```go
package main

import (
	"fmt"
	"io"
	"net/http"
)

func main() {
	http.HandleFunc("/", getRoot)
	http.HandleFunc("/hello", getHello)
	err := http.ListenAndServe(":8000", nil)
	if errors.Is(err, http.ErrServerClosed) {
		fmt.Println("Server closed")
	} else if err != nil {
		fmt.Printf("some error happened: %s", err)
		os.Exit(1)
	}
}

func getRoot(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /")
	io.WriteString(w, "This is / path")
}

func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /hello")
	io.WriteString(w, "This is /hello path")
}
```

Usually, you pass `nil` to the `http.ListenAndServe` method as its second parameter and then, the **Default Server Multiplexer** will handle incoming requests. For every call to the `http.HandlerFunc`, it be created a handler function with specified route in the default server multiplexer. The server multiplexer is an `http.Handler` that is able to look the incoming request path and call the proper handler function associated with that path. So in our example, it will call `getRoot` handler when client request `/` path and `getHello` handler for `/hello` path.

### Defining custom server multiplexer

In a large scale programs, using default server multiplexer could cause some issues and problems, and debugging could be hard and impossible. By defining a custom server multiplexer and use them in our HTTP server, we can have more control over the program.

Because `http.Handler` is an interface, we can define our server multiplexer but for creating a simple multiplexer, we can use basic implementation of multiplexer that `net/http` package provides for us. This basic multiplexer will call a handler function for a defined path.

#### Example

Using `http.ServeMux` we can have a basic server multiplexer that has not much differences with the default one, so there is no need to update the existing example too much:

```go
package main

import (
	"errors"
	"fmt"
	"io"
	"net/http"
	"os"
)

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", getRoot)
	mux.HandleFunc("/hello", getHello)
	err := http.ListenAndServe(":8000", mux)
	if errors.Is(err, http.ErrServerClosed) {
		fmt.Println("Server closed")
	} else if err != nil {
		fmt.Printf("some error happened: %s", err)
		os.Exit(1)
	}
}

func getRoot(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in / \n")
	io.WriteString(w, "This is / path")
}

func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /hello \n")
	io.WriteString(w, "This is /hello path")
}
```

Now, our server will behave like it do before, but this time you're using your own `http.Handler` instance instead of using the default one.

### Running multiple servers at once