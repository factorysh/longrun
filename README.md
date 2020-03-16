Long run
========

Handling long run REST call.

Goals
------

Handling long REST call, without blocking, without polling.

 * Distant RPC with HTTP
 * RPC can be "fire and forget" or streaming responses
 * RPC can be canceled
 * Server is async, client can be sync
 * Golang, python, javascript languages

Flow
----

### Polling

You can be old fashioned, with long polling.
It works with `curl`.

        | POST /{object}/      |
        | JSON body            |
    rpc |--------------------->|
        |  rpc_id, Location    | fast response with a unique ID with a 303 status
        |<---------------------|
        | GET /{object}/{id}   | ask for events
        | Last-Event-Id header | since 0
        |--------------------->|
        | [events]             | 
        |<---------------------|
        | GET /{object}/{id}   | ask for events
        | Last-Event-Id header | since last id
        |--------------------->|
        | [events]             | 
        ...

`last-event-id` can be a header, or a query argument.

The communication is async, the server doesn't wait client.
Client can only do the first step.
If client is slow, and server fast, the response will contain more than one events.

### Server Sent Event

        | POST /{object}/      |
        | JSON body            |
    rpc |--------------------->|
        |  rpc_id, Location    | fast response with a unique ID with a 303 status
        |<---------------------|
        | GET /{object}/{id}   | ask for events
        |--------------------->|
        | event                |
        |<---------------------|
        | event                |
        |<---------------------|
        | event                |
        |<---------------------|
        | event                |
        |<---------------------|
        ...

[SSE](https://html.spec.whatwg.org/multipage/server-sent-events.html) is a very simple streaming workflow : with a GET request, the response is a streamed text, using lines (with `\n`), and blank line for ending an event message.

Client ask for a run, with a POST http request, the arguments are a map, JSON serialized, in the body of the request. The JSON response return the run id.
The default response use a `201 Created` status. If the request has the header `accept: text/event-stream`, the response with a `303 See other` with a `Location: ` to the run endpoint.

### Methods

`POST` Ask something, it creates a run.

`GET` subscribe to run events. Multiple connections can watch a single run.

`HEAD` ask if a run exists.

`DELETE` cancel a run.

### Events

Events describe change in states.

Events are ordered, and typed (its state).

States are:

 * **queued** (implicit first state)
 * **running** (more than one running state is possible)
 * **canceled**
 * **error**
 * **success**

Attributes:

 * **id**: rank
 * **value**
 * **state**

Licence
-------

[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
