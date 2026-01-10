# HTTP (Hypertext Transfer Protocol)

Stateless application-layer protocol (TCP 80, or 443 for HTTPS) for retrieving web resources. Uses request-response model.

## Request-Response Model

```
1. Client initiates TCP connection
2. Client sends HTTP request (method + URI + headers + body)
3. Server processes request
4. Server sends HTTP response (status + headers + body)
5. Connection closes (or kept-alive)
```

## HTTP Methods

| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| GET | Retrieve resource | Yes | Yes |
| POST | Create/submit data | No | No |
| PUT | Replace resource | Yes | No |
| DELETE | Remove resource | Yes | No |
| HEAD | Like GET, no body | Yes | Yes |
| OPTIONS | Allowed methods | Yes | Yes |
| PATCH | Partial update | No | No |

## Status Codes

| Code | Meaning | Example |
|------|---------|---------|
| 1xx | Informational | 100 Continue |
| 2xx | Success | 200 OK, 201 Created |
| 3xx | Redirection | 301 Moved Permanently, 304 Not Modified |
| 4xx | Client error | 400 Bad Request, 404 Not Found, 403 Forbidden |
| 5xx | Server error | 500 Internal Server Error, 503 Service Unavailable |

## HTTPS (HTTP Secure)

- Encrypts HTTP over [[TLS]] (port 443)
- Prevents eavesdropping and tampering
- Requires digital certificate (X.509)
- Default for modern web (HTTP deprecated)

## Configuration (Web Server Publishing via NAT)

```
# Internal web server at 192.168.1.50:80
# Publish as 203.0.113.50:80 (NAT Server)

[Router-nat] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 80
[Router-nat] nat server protocol tcp global 203.0.113.50 443 inside 192.168.1.50 443

# Verify
display nat server
```

## Request Example

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
Accept-Encoding: gzip, deflate
Connection: keep-alive

[no body for GET]
```

## Response Example

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234
Cache-Control: max-age=3600
Set-Cookie: sessionid=abc123

<html>
  <head><title>Example</title></head>
  <body>Welcome</body>
</html>
```

## Performance Optimizations

### Keep-Alive (HTTP/1.1)
- Reuse TCP connection for multiple requests
- Reduces latency vs. new connection per request

### Compression
- gzip/deflate reduces response size
- Server: `Content-Encoding: gzip`
- Client: `Accept-Encoding: gzip`

### Caching
- ETags: Cache validation without re-download
- Last-Modified: Server tells client when resource changed
- Cache-Control: max-age, no-cache directives

### Content Delivery Network (CDN)
- Cache copies closer to users
- Reduces origin server load
- Geographically distributed

## Verification

```
# Test HTTP connectivity
[Router] curl http://www.example.com
[Router] curl -I http://www.example.com  # Headers only

# Monitor HTTP traffic
[Router] display traffic statistics interface GigabitEthernet0/0/0
```

## HTTP/2 and HTTP/3

- **HTTP/2**: Binary protocol; multiplexing; server push
- **HTTP/3**: Uses QUIC instead of TCP; faster, more reliable

## Related Concepts

- [[HTTPS]]
- [[TLS (Transport Layer Security)]]
- [[Web Server]]
- [[NAT Server]]
- [[DNS]]
- [[Network Services]]
