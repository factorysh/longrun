Long run
========

Handling long run rpc call.

Goals
------

Handling long rpc call, without blocking, without polling.

 * Local RPC with UNIX or TCP socket
 * Distant RPC with HTTP
 * RPC can be "fire and forget" or streaming responses
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

### Call

A call is a [Finite-state machine](https://en.wikipedia.org/wiki/Finite-state_machine).

A call has an unique ID, something like UUID v4 (random), hard to guess.

A call has a *Time To Live*.

A call can be canceled.

### Events

Events describe change in states.

Events are ordered, and typed (its state).

States are:

 * queued (implicit first state)
 * running (more than one running state is possible)
 * canceled
 * error
 * success

Attributes:

 * *id*: rank
 * *value*
 * *state*

Reference implementation
------------------------

[JSON-RPC2](https://www.jsonrpc.org/specification) with two transports : HTTP
and pascal string with UNIX socket.

Licence
-------

[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
