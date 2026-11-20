# http-client

HTTP/1.1 client for Onyx. It speaks plain HTTP and HTTPS, keeps a small connection pool, and follows redirects.

```onyx
use http.client {Client}

client := Client.{};
defer client->free();

result := client->get("https://example.com/");
if result.Err as e {
    printf("request failed: {}\n", e);
    return;
}

resp := result!;
defer resp->free();
printf("{}\n", resp.status);
```
