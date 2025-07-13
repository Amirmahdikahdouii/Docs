# Using Contexts with timeout in Go

One of the most important use cases of contexts in implementing services might be the context objects that has timeout for doing a process.
By using context with timeout, you can control *how much time one process need at most for execution.* This would be helpful for implementing reliable services and control latency in your code.

## Implementation

If you want to use `context.WithTimeout` to handle **HTTP requests** timeouts, you can easily use `net/http` package for managing the timeout for outgoing requests.

For creating a HTTP request, you can easily use `NewRequestWithContext` for setting a context with timeout manage requests timeout

```go
package main

import (
    "context"
    "log"
    "net/http"
    "time"
)

func main(){
    ctx, cancel := context.WithTimeout(context.TODO(), 5*time.Second)
    defer cancel()
    req, err := http.NewRequestWithContext(ctx, "POST", "https://example.com", nil)
	if err != nil {
		log.Println(err)
	}
	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		log.Println(err)
	}
}
```

In the code above, if the request need more than 5 second to be done, the code will return error.

### Implementation using channels and goroutine

For implementing such this behavior, you can use goroutine and channels. Imagine that we have a function that takes a `context.Context` as an argument. Then it process some behavior that takes time. But how we can notice that context timeout is exceeded or not?

```go
import (
    "context"
    "time"
)

func hugeProcess(ctx context.Context){
    // simulate huge process
    time.Sleep(10*time.Second)
}
```

In this code above, if you give the `hugeProcess` function a context with timeout less than 10 second, it would do nothing. It's totally true behavior, because you haven't tell the function to watch context timeout to terminate process!

Now lets update the code to watch context timeout as well:

```go
import (
    "context"
    "log"
    "time"
)

func handleHugeProcessTimeoutWithContext(ctx context.Context) {
    res := make(chan int, 1)
    go hugeProcess(res)
    select {
        case <-ctx.Done():
            log.Println("Context timeout exceeded")
        case <-res:
            log.Println("Process has done before reaching context timeout")
    }
}

func hugeProcess(res chan<-int) chan<- int {
    time.Sleep(10*time.Second)
    res<-1
    return res
}

func main(){
    ctx, cancel := context.WithTimeout(context.TODO(), 3*time.Second)
    defer cancel()
    handleHugeProcessTimeoutWithContext(ctx)
}
```

In the code above, the `hugeProcess` function would never end executing because it takes more time to finish than context timeout which is 3 second. And if we raise the timeout to 15 second for example, the `hugeProcess` function will return because it has less time for processing than timeout deadline.
