# GoBalancer - A Simple Load Balancer in Go

GoBalancer is a lightweight and efficient load balancer implemented in Go. It distributes incoming HTTP requests to multiple backend servers using a round-robin algorithm.

## Features
- **Reverse Proxy Support**: Uses `httputil.ReverseProxy` to forward requests to backend servers.
- **Round-Robin Load Balancing**: Distributes incoming requests evenly among available servers.
- **Health Check (Basic)**: Skips forwarding requests to unresponsive servers.
- **Logging**: Logs request forwarding details for better debugging.
- **Customizable Backend Servers**: Easily configure backend server addresses in the code.

## Installation
Ensure you have Go installed on your system. If not, download and install it from [golang.org](https://golang.org/).

### Clone the repository
```sh
$ git clone <repository-url>
$ cd <repository-folder>
```

## Usage
### Running GoBalancer
1. Open a terminal and navigate to the project folder.
2. Run the following command to start GoBalancer:
   ```sh
   $ go run main.go
   ```
3. The load balancer will start listening on `localhost:3000`.

### Configuration
- GoBalancer currently forwards requests to three predefined servers:
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
Defines the basic structure of a server that GoBalancer interacts with.

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

### Start GoBalancer
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
Once GoBalancer is running, send a request using curl:
```sh
$ curl http://localhost:3000
```
It will forward the request to one of the configured backend servers.

## License
This project is open-source and available under the MIT License.

