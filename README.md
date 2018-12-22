Long run
========

Handling long run rpc call.

Goals
------

 * Local RPC with UNIX or TCP socket
 * Distant RPC with HTTP
 * RPC can be fire and forget or streaming response
 * RPC can be canceled
 * Server is async, client can be sync
 * Golang, python, javascript languages

Flow
----

    rpc |--------------->|
        |  rpc_id        | fast response with a unique ID
        |<---------------|
        | next(rpc_id, 0)| ask for events, since 0
        |--------------->|
        | [events]       | wait for new event, events can be a batch of events
        |<---------------|
        | next(rpc_id, n)| ask for new events
        |--------------->|
        | [events]       | more events
        |<---------------|
        ...


Reference implementation
------------------------

[JSON-RPC2](https://www.jsonrpc.org/specification) with two transports : HTTP
and pascal string with UNIX socket.