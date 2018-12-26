# bocadillo.routing

## Route
```python
Route(self, pattern: str, view: Callable[[bocadillo.request.Request, bocadillo.response.Response, dict], Coroutine], methods: List[str], name: str)
```
Represents the binding of an URL pattern to a view.

As a framework user, you will not need to create routes directly. This
should be done via `@api.route()`.

__Parameters__

- __pattern (str)__: an URL pattern. F-string syntax is supported for parameters.
- __view (coroutine function)__:
    A view given as a coroutine function. Non-async views (synchronous,
    class-based) will have to have been converted beforehand.
- __methods (list of str)__:
    A list of (upper-case) HTTP methods.
- __name (str)__:
    The route's name.

### url
```python
Route.url(self, **kwargs) -> str
```
Return full path for the given route parameters.

__Parameters__

- __kwargs (dict)__: route parameters.

__Returns__

`url (str)`:
    A full URL path obtained by formatting the route pattern with
    the provided route parameters.

### parse
```python
Route.parse(self, path: str) -> Union[dict, NoneType]
```
Parse an URL path against the route's URL pattern.

__Returns__

`result (dict or None)`:
    If the URL path matches the URL pattern, this is a dictionary
    containing the route parameters, otherwise None.

### raise_for_method
```python
Route.raise_for_method(self, req: bocadillo.request.Request)
```
Fail if the requested method is not supported by the route.

__Parameters__

- __req (Request)__: a `Request` object.

__Raises__

- `HTTPError(405)`: if `req.method` is not one of `methods`.

## check_route
```python
check_route(pattern: str, view: Union[Callable[[bocadillo.request.Request, bocadillo.response.Response, dict], Coroutine], bocadillo.views.ClassBasedView], methods: List[str]) -> None
```
Check compatibility of a route pattern and a view.

__Parameters__

- __pattern (str)__: an URL pattern.
- __view__: a function-based or class-based view.
- __methods__: an upper-cased list of HTTP methods.

__Raises__

- `RouteDeclarationError `:
    - If one of the `methods` is not a member of `ALL_HTTP_METHODS`.
    - If `pattern` does not have a leading slash.
    - If the `view` does not accept at least two positional arguments
    (for the request and the response objects).
    - If route parameters declared in the pattern do not match those
    used on the view, e.g. a parameter is declared in the pattern, but
    not used in the view or vice-versa.

__See Also__

- `ALL_HTTP_METHODS` in [constants](./constants.md)

## RouteMatch
```python
RouteMatch(self, /, *args, **kwargs)
```
Represents the result of a successful route match.
### params
Alias for field number 1
### route
Alias for field number 0
## Router
```python
Router(self)
```
A collection of routes.
### add_route
```python
Router.add_route(self, view: Union[Callable[[bocadillo.request.Request, bocadillo.response.Response, dict], Coroutine], bocadillo.views.ClassBasedView], pattern: str, *, methods: List[str] = None, name: str = None, namespace: str = None) -> bocadillo.routing.Route
```
Build and register a route.

__Parameters__

- __view__: a function-based or class-based view.
- __pattern (str)__: an URL pattern.
- __methods (list of str)__:
    An optional list of HTTP methods.
    Defaults to `["get", "head"]`.
    Ignored for class-based views.
- __name (str)__:
    An optional name for the route.
    If a route already exists for this name, it is replaced.
    Defaults to a snake-cased version of the view's `__name__`.
- __namespace (str)__:
    An optional namespace for the route. If given, it is prefixed
- __to the name and separated by a colon, i.e. `{namespace}__:{name}`.

__Returns__

`route (Route)`: the newly registered `Route` object.

__Raises__

- `RouteDeclarationError`:
    If route validation has failed.

__See Also__

- [check_route](#check-route) for the route validation algorithm.

### route
```python
Router.route(self, *args, **kwargs)
```
Register a route by decorating a view.

__See Also__

- [add_route](#add-route)

### match
```python
Router.match(self, path: str) -> Union[bocadillo.routing.RouteMatch, NoneType]
```
Attempt to match an URL path against one of the registered routes.

__Parameters__

- __path (str)__: an URL path.

__Returns__

`match (RouteMatch or None)`:
    A `RouteMatch` object built from a route that matched against
    `path` and the extracted route parameters, or `None` if none
    matched.

### get_route_or_404
```python
Router.get_route_or_404(self, name: str) -> bocadillo.routing.Route
```
Return a route or raise a 404 error.

__Parameters__

- __name (str)__: a route name.

__Returns__

`route (Route)`: the `Route` object registered under `name`.

__Raises__

- `HTTPError(404)`: if no route exists for the given `name`.

## RoutingMixin
```python
RoutingMixin(self)
```
Provide routing capabilities to a class.
### route
```python
RoutingMixin.route(self, pattern: str, *, methods: List[str] = None, name: str = None, namespace: str = None)
```
Register a new route by decorating a view.

This is an alias to the underlying router's `route()` decorator.

__See Also__

- [Router.route](/api/routing.md#route-3)

### url_for
```python
RoutingMixin.url_for(self, name: str, **kwargs) -> str
```
Build the URL path for a named route.

__Parameters__

- __name (str)__: the name of the route.
- __kwargs (dict)__: route parameters.

__Returns__

`url (str)`: the URL path for a route.

__Raises__

- `HTTPError(404) `: if no route exists for the given `name`.
