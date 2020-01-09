# websocket

[![GoDoc](https://godoc.org/github.com/schollz/websocket?status.svg)](https://godoc.org/github.com/schollz/websocket)

websocket is a minimal and idiomatic WebSocket library for Go.

Fork of https://github.com/nhooyr/websocket

## Install

```bash
go get github.com/schollz/websocket
```

## Features

- Minimal and idiomatic API
- Tiny codebase at 2200 lines
- First class [context.Context](https://blog.golang.org/context) support
- Thorough tests, fully passes the [autobahn-testsuite](https://github.com/crossbario/autobahn-testsuite) (see https://github.com/nhooyr/websocket)
- [Zero dependencies](https://godoc.org/github.com/schollz/websocket?imports)
- JSON helpers in the [wsjson](https://godoc.org/github.com/schollz/websocket/wsjson)
- Highly optimized by default
- Concurrent writes out of the box
- [Complete Wasm](https://godoc.org/github.com/schollz/websocket#hdr-Wasm) support
- [Close handshake](https://godoc.org/github.com/schollz/websocket#Conn.Close)

## Examples

For a production quality example that shows off the full API, see the [echo example on the godoc](https://godoc.org/github.com/schollz/websocket#example-package--Echo). On github, the example is at [example_echo_test.go](./example_echo_test.go).

Use the [errors.As](https://golang.org/pkg/errors/#As) function [new in Go 1.13](https://golang.org/doc/go1.13#error_wrapping) to check for [websocket.CloseError](https://godoc.org/github.com/schollz/websocket#CloseError).
There is also [websocket.CloseStatus](https://godoc.org/github.com/schollz/websocket#CloseStatus) to quickly grab the close status code out of a [websocket.CloseError](https://godoc.org/github.com/schollz/websocket#CloseError).
See the [CloseStatus godoc example](https://godoc.org/github.com/schollz/websocket#example-CloseStatus).

### Server

```go
http.HandlerFunc(func (w http.ResponseWriter, r *http.Request) {
	c, err := websocket.Accept(w, r, nil)
	if err != nil {
		// ...
	}
	defer c.Close(websocket.StatusInternalError, "the sky is falling")

	ctx, cancel := context.WithTimeout(r.Context(), time.Second*10)
	defer cancel()

	var v interface{}
	err = wsjson.Read(ctx, c, &v)
	if err != nil {
		// ...
	}

	log.Printf("received: %v", v)

	c.Close(websocket.StatusNormalClosure, "")
})
```

### Client

```go
ctx, cancel := context.WithTimeout(context.Background(), time.Minute)
defer cancel()

c, _, err := websocket.Dial(ctx, "ws://localhost:8080", nil)
if err != nil {
	// ...
}
defer c.Close(websocket.StatusInternalError, "the sky is falling")

err = wsjson.Write(ctx, c, "hi")
if err != nil {
	// ...
}

c.Close(websocket.StatusNormalClosure, "")
```

## LICENSE

MIT (Anmol Sethi)
