---
id: version-0.9.1-nimo
title: Introduction
sidebar_label: Introduction
original_id: nimo
---

Nimo helps you to organize your middlewares (and handlers).

## Installation

```sh
composer require litphp/nimo
```

## MiddlewarePipe

Pipe middlewares one after another, is the essential part of middleware pattern. And it's the core infrastructure of nimo.

```php
$pipe = new MiddlewarePipe();

$pipe->append($middleware); // append $middleware to last of the pipeline

$pipe->prepend($middleware2); // also you can do prepend
```

Thanks to our `MiddlewareTrait`, you can invoke `prepend` and `append` on any middleware instance provided by us. (or yours, just use the trait)

```php
$first = new NoopMiddleware();

$first->append($second); // same as (new MiddlewarePipe())->append($first)->append($second)
```

### `$next` passed to middleware

Prior to 0.9.1 the MiddlewarePipe is implemented with a mutable state inside. If any middleware call `$next` more than one times, the behavior will be unreasonable (it increase inner `$index` and try to call the middleware at that index).

Start from 0.9.1, the state is immutable, makes calling `$next` always to invoke remaining stack of middleware(s) as expected. This can be useful for retry logic or fallback logic.

> If you are implementing some middleware, be careful to keep request related state in request object only. Don't save any request state in middleware object or calling `$next` twice may be broken due to leaked state.

## Dummys

There are some basic middleware/handlers with straightforward behavior.

- `NoopMiddleware`: middleware doing nothing but delegate to the handler
- `FixedResponseMiddleware`: never call handler, just return that fixed response
- `FixedResponseHandler`: just return that fixed response
- `CallableHandler`: wraps a callable with signature `(RequestInterface): ResponseInterface` 
- `MiddlewareIncludedHandler`: include a middleware into a handler (`HandlerTrait::includeMiddleware` helps you do this directly on handler instance)
