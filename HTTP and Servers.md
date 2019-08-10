# HTTP and Servers

- Error code 404 - Resource not found
- Inbuilt python demo Web server:
  - _`python3 -m http.server 8000`_
- [***Error Codes - w3.org***](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
- ***A server is just a program that accepts connections from other programs on the network***
- A web address is also called a URI for Uniform Resource Identifier
- The term URL - Uniform Resource Locator. A URL is a URI for a resource on the network
- `URI` - is a name for a resource
  - _`URI`'s are made of several different parts_
  - Each part has its own syntax
  - A `URI` - `https://en.wikipedia.org/wiki/Fish` is constructed out of
    - ***SCHEME*** : _https_
    - ***HOSTNAME***: _en.wikipedia.org_
    - ***PATH***: _/wiki/Fish_

## SCHEME

- The first part of a URI is the scheme
- Tells the client how to go about accessing the resource
- [***Various URI Schemes - other than `http, https, file`***](https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml)
- __

## HOSTNAME

- Tells the client which server to connect to
- A hostname ***can only appear after a URI scheme that supports it***
- NOT EVERY URI HAS A HOSTNAME : `mailto:spam@example.net`

> Note:The symbol `:` goes after the SCHEME ans `//` goes before the HOSTNAME

## PATH

- In an `HTTP URI` (and many others), the `path`, identifies a particular ***(`conceptual`)*** `resource` on a server
  - A server can have many ***(conceptual) resources*** on it
  - Example - web pages, videos, or APIs
  - `path` tells the server which `resource` `the client` is looking for
  - Default `path` is the root `/`

> NOTE: For instance, if you do a Google search, you'll see a `URI path` such as `/search?q=ponies`. ***This `doesn't mean` that there's literally a file on a server at Google with a filename of `search?q=ponies`.*** The server interprets the path to figure out what resource to send

### ***URI such as these `<a href="cliffsofinsanity.png">cliffsofinsanity.png</a>`***

- This URI, value of `href`, doesn't have `scheme` or a `host-name`
- This is a ***relative URI reference***
- This URI doesn't include the `hostname or port` of the server it's on, but the browser can figure that out from context

### `https://en.wikipedia.org/wiki/Oxygen#Discovery`

- `#` is called the **`fragment`**
- The browser doesn't even send it to the web server
- Browser lets a link point to a `specific named` part of a resource

[More Info](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier#Generic_syntax)

<hr/>

## HostNames

- A hostname in a URI can also be an `IP address`

> NOTE: Why is it called a hostname? In network terminology, a host is a computer on the network; one that could host services

- To connect to a web server like `www.google.com`, a client needs to translate the hostname into an `IP address`
- Client operating system's `network configuration` uses the `Domain Name Service (DNS)`  to fetch the `IP address`
- `DNS` - a set of servers maintained by Internet Service Providers (ISPs) and other network users — to look up `hostnames` and get back `IP addresses`

> ONe can make a DNS lookup query through `nslookup` and `host` commands

### Localhost

- IPv4 - `127.0.0.1` - `{00000000.00000000.00000000.00000001}` - 4 octets
- IPv6 - `::1` - 16 octets

> Note: A single web server can have lots of different web sites running on it, each with their own hostname. When a client asks the server for a resource, it has to specify what hostname it intends to be talking to, through HTTP headers

<hr/>

## Ports

- Most of the web addresses don't have the port numbers on them
- client usually ***figures*** out the port number from the `URI scheme (http, https etc)`
  - `HTTP` `URIs` imply a `port number` of `80`
  - `HTTPS` `URIs` imply a `port number` of `443`

### Port Numbers ? o_O)

- Network traffic that computers send and receive  is split up into messages called `packets` 
- Each packet has the `IP addresses`
  - of the computer that `sent it`
  - and the computer that `receives it`
- Each packet has the `port number` for the `sender` and `recipient`
- ***port numbers distinguish programs on the computers, which are interacting with the network***

> IP addresses distinguish computers; port numbers distinguish programs on those computers

> Server **`Listening on a port`** means that when the server starts up, it tells its `operating system` that it wants **to receive connections** from clients on a `particular port number` When a client (such as a web browser) "connects to" that port and sends a request, ***the operating system knows to forward that request to the server that's listening on that port***

<hr/>

## HTTP Request => HTTP VERBS - `GET`

- Type:

```js

ncat 127.0.0.1 8000

```

- Then Type
  - HTTP request line

```js
    GET / HTTP/1.1
    Host: localhost
```

- _`GET`_ is the verb
- _`/`_ is the resource path - in this case the default resource
- _`HTTP/1.1`_ is the protocol


## HTTP Response

- HTTP Response is made of three parts
  - _The Status Line_
  - _Some Headers_
  - _Response Body_

  ```js
    HTTP/1.0 200 OK   /** The status line  */
    Server: SimpleHTTP/0.6 Python/3.7.4 /*  Start - Some Headers    */
    Date: Fri, 09 Aug 2019 12:20:13 GMT
    Content-type: text/html
    Content-Length: 84
    Last-Modified: Thu, 08 Aug 2019 18:20:40 GMT /*  End - Some Headers    */


    /* Response Body below */

    <html>
      <body>
         <span>
            hello world !! index.html
         </span>
     </body>
    </html>
  ```

- The `status line` is the first line of text that the server sends back
- The `headers` are the other lines up until the first blank line
- The `response body` is the rest — in this case, it's a piece of HTML

### Status line

- The `status line` tells the client whether the server understood the request
- Whether the server has the resource the client asked for and how to proceed next 
- Also tells the client which dialect of HTTP the server is speaking

#### ***Status Line - Status Codes***

- **HTTP/1.0 `200` OK** - The status line - `200` is the status code
- ***The first digit*** of the status code indicates the general success of the request
  - `1xx — Informational`-  The request is in progress or there's another step to take
  - `2xx — Success!` - The request succeeded. The server is sending the data the client asked for
  - `3xx — Redirection` -  The server is telling the client a different URI it should redirect to  
    - The Response `headers` will contain a `Location` header with the updated URI
    - Different codes tell the client whether a _redirect is permanent or temporary_
  - `4xx — Client error` -  The server didn't understand the client's request, or can't or won't fill it
    - Different `4xx` codes tell the client whether
      - it was a bad URI
      - a permissions problem 
      - another sort of error.
  - `5xx — Server error` -  Something went wrong on the server side

> **[Different Status Codes](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)**

> launch query `ncat 172.217.167.132 80` on terminal then type the following:

```js
POST / HTTP/1.1
Host: www.google.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 32

deep=dee&shix=welcomeswordwordword
```

> Response is the following

```js
HTTP/1.1 405 Method Not Allowed
Allow: GET, HEAD
Date: Fri, 09 Aug 2019 14:06:53 GMT
Content-Type: text/html; charset=UTF-8
Server: gws
Content-Length: 1589
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
```

<hr/>

### HEADERS

- An HTTP response can include many headers
  - Each header is a line that starts with a `keyword`, such as `Location` or `Content-type`, 
  - followed by a `colon` - `:` and a `value`
- `Headers` are a sort of `metadata` for the `response`
- ***They aren't displayed by the browsers or other clients***
- ***They tell the client various information about the response***

> Many, many features of the Web are implemented using headers for example `cookies`

- `cookies` are a Web feature that lets servers `store data` on the browser
- To **set a cookie**, the server sends the `Set-Cookie` header  
  - The browser will then send the **cookie** data back in a `Cookie` header on subsequent requests
  - **ITS THE BROWSER WHICH SENDS THE COOKIE NO OTHER CLIENT**

### RESPONSE BODY

- The `headers` end with a blank line. Everything **after that** blank line is part of the `response body`
- If the `request` was `successful` (a `200 OK` **status**, for instance), ***the response is a copy of whatever resource the client asked for*** 
  - such as a web page, image, or other pieces of data - [ A resource represented as text ]
- In the case of an ***error***, the `response body` is where the `error message goes`



<hr/>

## TO BE CONTINUED ....
