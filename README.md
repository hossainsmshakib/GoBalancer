# Load Balancer in Go

This is a simple load balancer implementation in Go that forwards requests to different backend servers using a round-robin algorithm.

## Features
- Implements a reverse proxy using `httputil.ReverseProxy`.
- Uses round-robin load balancing to distribute requests across multiple servers.
- Checks if a server is alive before forwarding requests.
- Logs forwarded requests.

## Installation
Ensure you have Go installed on your system. If not, download and install it from [golang.org](https://golang.org/).

### Clone the repository
```sh
$ git clone <repository-url>
$ cd <repository-folder>
```

## Usage
### Running the Load Balancer
1. Open a terminal and navigate to the project folder.
2. Run the following command to start the load balancer:
   ```sh
   $ go run main.go
   ```
3. The load balancer will start listening on `localhost:3000`.

### Configuration
- The load balancer currently forwards requests to three predefined servers:
  - Facebook (`https://www.facebook.com`)
  - Bing (`https://www.bing.com`)
  - DuckDuckGo (`https://www.duckduckgo.com`)
- You can modify the `servers` slice in `main.go` to add or remove backend servers.

## Code Overview
### `Server` Interface
```go
type Server interface {
    Address() string
    IsAlive() bool
    Serve(rw http.ResponseWriter, req *http.Request)
}
```
Defines the basic structure of a server that the load balancer interacts with.

### `simpleServer` Struct
```go
type simpleServer struct {
    addr  string
    proxy *httputil.ReverseProxy
}
```
Represents a simple reverse proxy server.

### Load Balancer Logic
- Uses round-robin for load balancing.
- Implements the `getNextAvailableServer` method to find the next available server.
- The `serveProxy` function forwards the request to the selected server.

### Start the Load Balancer
```go
func main() {
    servers := []Server{
        newSimpleServer("https://www.facebook.com"),
        newSimpleServer("https://www.bing.com"),
        newSimpleServer("https://www.duckduckgo.com"),
    }

    lb := NewLoadBalancer("3000", servers)
    handleRedirect := func(rw http.ResponseWriter, req *http.Request) {
        lb.serveProxy(rw, req)
    }

    http.HandleFunc("/", handleRedirect)
    fmt.Printf("serving requests at 'localhost:%s'\n", lb.port)
    http.ListenAndServe(":"+lb.port, nil)
}
```

## Example Request
Once the load balancer is running, send a request using curl:
```sh
$ curl http://localhost:3000
```
It will forward the request to one of the configured backend servers.

## License
This project is open-source and available under the MIT License.

