---
title: "Golang net/http for building a rest server"
---
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

The `net/http` package also allows you to have your own http server for better customization. You may want to change the default behavior of how server runs, or you want to have multiple server in a same program at once.

We want to change our example to have 2 multiple servers running at once, then We want to see the default behavior of the servers by logging them in the terminal:

```go
package main

import (
	"context"
	"errors"
	"fmt"
	"io"
	"net"
	"net/http"
)

const keyServerAddr string = "serverAddr"

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", getRoot)
	mux.HandleFunc("/hello", getHello)
	ctx, cancel := context.WithCancel(context.Background())

	contextHandler := func(l net.Listener) context.Context {
		ctx = context.WithValue(ctx, keyServerAddr, l.Addr().String())
		return ctx
	}

	serverOne := &http.Server{
		Addr:        ":3333",
		Handler:     mux,
		BaseContext: contextHandler,
	}

	serverTwo := &http.Server{
		Addr:        ":4444",
		Handler:     mux,
		BaseContext: contextHandler,
	}

	go func(){
		err := serverOne.ListenAndServe()
		if errors.Is(err, http.ErrServerClosed){
			fmt.Println("Server one closed")
		}else if err != nil {
			fmt.Printf("error listening on server one")
		}
		cancel()
	}()

	go func(){
		err := serverTwo.ListenAndServe()
		if errors.Is(err, http.ErrServerClosed){
			fmt.Println("Server two closed")
		}else if err != nil {
			fmt.Printf("error listening on server two")
		}
		cancel()
	}()

	<-ctx.Done()
}

func getRoot(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()
	fmt.Printf("Serer Addr:  %v, Got request in / \n", ctx.Value(keyServerAddr))
	io.WriteString(w, "This is / path")
}

func getHello(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()
	fmt.Printf("Serer Addr:  %v, Got request in /hello \n", ctx.Value(keyServerAddr))
	io.WriteString(w, "This is /hello path")
}
```

Now, we have defined our http servers and instead of calling `http.ListenAndServe` function, we use `http.Server.ListenAndServe` function but without any given parameters.

We also have customized `BaseContext` parameter in both http servers to include an extra context key values to storing the server addr, for the server that receive requests and then print them in the terminal. If we use curl to request the `/` path in both servers, then we have this output printed in terminal:

```sh
curl http://localhost:3333/
curl http://localhost:4444/
```

Output:

```log
Serer Addr:  [::]:3333, Got request in / 
Serer Addr:  [::]:4444, Got request in /
```

---

### Working with Query Strings

```go
package main

import (
	"context"
	"errors"
	"fmt"
	"io"
	"net"
	"net/http"
)

const keyServerAddr string = "serverAddr"

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", getRoot)
	mux.HandleFunc("/hello", getHello)
	ctx := context.Background()

	contextHandler := func(l net.Listener) context.Context {
		ctx = context.WithValue(ctx, keyServerAddr, l.Addr().String())
		return ctx
	}

	server := &http.Server{
		Addr:        ":3333",
		Handler:     mux,
		BaseContext: contextHandler,
	}

	err := server.ListenAndServe()
	if errors.Is(err, http.ErrServerClosed) {
		fmt.Println("Server closed")
	} else if err != nil {
		fmt.Printf("error listening on server")
	}
}

func getRoot(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in / \n")
	io.WriteString(w, "This is / path")
}

func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /hello \n")

	name := "anoymous"
	if r.URL.Query().Has("name") {
		name = r.URL.Query().Get("name")
	}
	io.WriteString(w, fmt.Sprintf("Hello %s!", name))
}
```

In this example, we have accessed the Query parameters using `r.URL.Query` and it's defined method. We use `Has` and `Get` method for checking the existance of a key in a query parameters and then retrieve it.

> [!NOTE]
> The `Get` method will return an empty string if the key is not defined in a query parameters, but sometimes you may want to stricktly check the query existance for redirecting for change results in response using `Has` method.

---

### Reading data from request body

```go
package main

import (
	"context"
	"errors"
	"fmt"
	"io"
	"net"
	"net/http"
)

const keyServerAddr string = "serverAddr"

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", getRoot)
	mux.HandleFunc("/hello", getHello)
	ctx := context.Background()

	contextHandler := func(l net.Listener) context.Context {
		ctx = context.WithValue(ctx, keyServerAddr, l.Addr().String())
		return ctx
	}

	server := &http.Server{
		Addr:        ":3333",
		Handler:     mux,
		BaseContext: contextHandler,
	}

	err := server.ListenAndServe()
	if errors.Is(err, http.ErrServerClosed) {
		fmt.Println("Server closed")
	} else if err != nil {
		fmt.Printf("error listening on server")
	}
}

func getRoot(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in / \n")
	io.WriteString(w, "This is / path")
}

func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /hello \n")
	data, err := io.ReadAll(r.Body)
	if err != nil {
		fmt.Printf("failed to read data from body: %s", err)
	}
	fmt.Printf("Data: %v", string(data))
	io.WriteString(w, "Hello World!")
}
```

By using `r.Body` and `io.ReadAll` function, we could read data from request body, then work with data, in this case I print the result in terminal:

```sh
curl -X POST -d '{"name": "Amir"}' http://localhost:3333/hello
```

```log
Got request in /hello 
Data: {"name": "Amir"}
```

We also may want to work with Form data in our handler. For retrieve form value with a specified name, we can use `PostFormValue()` method from `*http.Request` object and pass the key argument for getting the value:

```go
func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /hello \n")
	name := "anonymous"
	if r.PostFormValue("name") != "" {
		name = r.PostFormValue("name")
	}
	fmt.Printf("Name: %s", name)
	io.WriteString(w, "Hello World!")
}
```

```sh
curl -X POST -F 'name=Amir' http://localhost:3333/hello
```

```log
Got request in /hello
Name: Amir
```

> [!NOTE]
> The `r.PostFormValue` only will work on form data incoming with the `POST` HTTP method. We have another method for retrieve form data both from forms that are incoming using `GET` method (Placed in query string) and `POST` method, named `r.FormValue`.
> This method is used for getting form data when you're dealing with getting form data from query string.
> **Using this method, could override data if both keys are available in query string and post method form data, be noticed about it**

### Set status code and header in response

```go
func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("Got request in /hello \n")
	var name string
	if r.PostFormValue("name") != "" {
		name = r.PostFormValue("name")
	} else {
		w.Header().Set("x-missing-form", "name")
		w.WriteHeader(http.StatusBadRequest)
		return
	}
	fmt.Printf("Name: %s", name)
	io.WriteString(w, "Hello World!")
}
```

By using `w.Header().Set()`, you can write a header for your response to the client. 

A header field is a key and value that either a client or server will send to the other to let them know about themselves. There are many headers that are pre-defined by the HTTP protocol, such as `Accept`, which a client uses to tell the server the type of data it can accept and understand. It’s also possible to define your own by prefixing them with `x-` and then the rest of a name.

Also you can write status code for response to be returned to the client, using `w.WriteHeader()` method and passing an status code. Here we used constant status codes that are available in http package.

```sh
curl -v -X POST http://localhost:3333/hello
```

```log
* Uses proxy env variable no_proxy == 'localhost'
*   Trying 127.0.0.1:3333...
* Connected to localhost (127.0.0.1) port 3333 (#0)
> POST /hello HTTP/1.1
> Host: localhost:3333
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 400 Bad Request
< X-Missing-Form: name
< Date: Sat, 28 Jun 2025 09:30:43 GMT
< Content-Length: 0
< 
* Connection #0 to host localhost left intact
```

Now you can see it you not sending data using a `name` form field to the client using the HTTP `POST` method, application will response with **bad request status code** and `x-missing-field` for missing form field. 

> [!NOTE]
> the `r.Method` property in `*http.Request` parameter, has the http method for request

```go
fmt.Println(r.Method)
// GET
// POST
// PUT
// DELETE
// ...
```

