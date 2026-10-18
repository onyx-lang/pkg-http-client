# http-client

HTTP/1.1 client for Onyx. It speaks plain HTTP and HTTPS, keeps a small connection pool, and follows redirects.

```onyx
use http

client := http.client.Client.{};
defer client->free();

result := client->get("https://example.com/");
if result.Err {
    printf("request failed: {}\n", result->err()!);
    return;
}

resp := result!;
defer resp->free();
printf("{}\n", resp.status);
```

`get`, `post`, `Client.send`, and `RequestBuilder.send` return `Result(&Response, Error)`. Free a successful response with `resp->free()`.

## Timeouts

`Client.default_timeout` and `Request.timeout` are `time.Duration` values. The default is 10 seconds. The deadline covers reading the response. Connecting, DNS, and the TLS handshake are still blocking.

## Redirects

`Client.max_redirects` defaults to 10. Set it to 0 to disable following. `301`, `302`, and `303` are resent as `GET` without a body. `307` and `308` keep the method and body. `Authorization` is removed when the host changes.

## Cookies

The client stores `Set-Cookie` on the client jar. A cookie with no `Domain` is host-only. `Domain` must match the response host. `Path`, `Secure`, `Max-Age`, and `Expires` are honored. Cookies set with `CookieJar.set` and no domain are sent to every host.

## TLS

HTTPS verifies the certificate chain and the hostname. Connect and handshake timeouts are not applied.
