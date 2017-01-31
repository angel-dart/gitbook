Requests in the Angel framework go through a relatively complex lifecycle, and to truly master the framework, one must understand that lifecycle.

1. `startServer` is called.
2. Each `HttpRequest` is sent through `handleRequest`.
3. `beforeProcessed` is fired with the `HttpRequest`.
4. `handleRequest` converts the `HttpRequest` to a `RequestContext`, and converts its `HttpResponse` into a
`ResponseContext`.
5. `angel_route` is used to match the request path to a list of request handlers.
6. `before` and `after` are combined with the handler list.
7. Each handler is executed.
8. `afterProcessed` is fired with the `HttpRequest`.
9. *All* `responseFinalizers` are run, if `res.willCloseItself != true`.
10. If `res.willCloseItself = false`, all headers, the status code and the response buffer are sent through the actual `HttpResponse`.
11. The `HttpResponse` is closed.

If at any point an error occurs, Angel will catch it. See the [error handling](https://github.com/angel-dart/angel/wiki/Error-Handling) docs for more.