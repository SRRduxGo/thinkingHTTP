# HTTP

> Hyper Text Transfer Protocol : Sending / recieving the text is the motto

- Request Section
  - 1'st line gives the request URL and type of request
  - Header Section
    - `Host` header is the **`Required header`**
    - Headers is the `Meta Data` for the request itself

- Response Section
  - 1'st line has the `status code`
  - Header Section
    - `Content-length` header is the **`Required`** , gives number of bytes in the response
    - Headers is the `Meta Data` for the response itself
  - After the Header section there is one _`BLANK line`_
  - _`Actual document after the BLANK line`_

> Side note: For a `POST` request backend should always send a `re-direct` response as the `POST` requests are allowed to be distuctive operations
> **`Post -> 303 see other -> Get`**

## _`XHR`_ VS _`fetch`_ API

- _`XHR`_ verbose compared to _`fetch`_
- _`fetch`_ is promised based

## The **_`fetch`_** [[MDN]](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

- _The **Fetch API** provides a JavaScript interface for accessing and manipulating parts of the `HTTP pipeline`, such as requests and responses_
- _Fetch provides a single logical place to define  HTTP-related concepts such as CORS and extensions to HTTP_
- _The Promise returned from `fetch()` **won’t reject on HTTP error status**_
  - _It will only reject on network failure or if anything prevented the request from completing_
  - _By default, `fetch` **won't send or receive any cookies**_
  - _to send cookies, the [**`credentials init`**](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) option must be set_
  

## HTTP Verbs

- HTTP request-text begins with a unique `verb`
  - `HEAD` verb can be used to fetch `Meta` data of the `HTTP resource` `{content-length, last-modified}`
  - Every `request-response` pair is called round-trip (time consuming)
  - _`OPTIONS` verb gives you all the Methods / Request-Verbs supported on the current URL. Not all servers support `OPTIONS`. It is important in implementing CORS_

## REST - Representational State Transfer

- Basic entities are Collections and objects inside those Collections
  - Basic rest request `Get <CollectionName>/<ObjectName>`
  - `GET PUT POST DELETE` Rest focused verbs
  - `REST` is a sort of a `conceptual framework` achieved using `HTTP` requests

## Performance

- Network Stack - `HTTP/TCP/IP/Ethernet`
  - IP allows us to talk to other Machines
  - TCP allows us to have **_`multiple-independent`_** `data streams` between the Machines
  - These streams are identified by the `port numbers`
  - Creating / Opening a connection is costly as it requires three way handshake
  - IF HTTPS the another three way TLS handshake will also be required.
- TTFB (Time to fist byte - waiting time before first byte is recieved)
- ***Head-of-line blocking***
  - Usually HTTP requests forms a Queue - and Head-of-line request can become a bottle-neck
  - To deal with this bottle-neck browsers allow six parallel connections

- ***Keep-Alive***
  - Every-time the browser connects to a server to make a request, browser has to go through a three way handshake-process - very time consuming
  - To mitigate this problem - HTTP/1.1 - client can introduce a header `Connection: keep-alive`
    - Server will not close the connection after successsfully delievering the response (usually connection drops after request-response success)
    - Same connection can be used to deliver other requests

## HTTP**S**

- Requests are Encrypted
- Read about `MITM` - `Man-In-the-Middle` attack
- ***`HTTPS = HTTP + TLS`*** 

> TLS = Transport Layer Security

- TLS can be used by any protocol - ex - FTP + TLS = FTPS
- Close to impossible to break TLS encryption
- TLS utilises `chain of Trust`
  - Server Identifies itself with a `Certificate`
    - ***`Certificate = MetaData about the Server + Fingerprint of an Encryption Key`***
    - This `Certificate` are issued by one of the limited **`authorities`**
    - **_CANNOT BE ARIBITRARILY MANUFACTURED_**

## TLS

- *Has two **Cryptographic** Building Blocks*
  - `Encryption + Hashing`
    - `Encryption`
      - Public Key + Private Key combination for encrypt and decrypt
      - Public Key encryption is called Assymetric Encryption
    - `Hashing`
      - Is a process of Transforming Data into a ***`Short Representation of the Original Data`***
      - `Hash` value cannot be reversed back to original data
      - ***IF TWO DATA ITEMS PRODUCE SAME HASH VALUE THEN IT IS `VERY VERY LIKELY { there is still some margin of error }` THAT BOTH THE DATA ITEMS ARE THE SAME***
      - `Hash Function` should have above said properties ;) otherwise big FUCKUPS!!
        - SHA1 { number says how big the output of Hash is in BITS }
        - SHA256
        - SHA512

## Signatures

- Server signs a document and encrypts it with Private Key = Signed Document
- Documents can become large -> Encrypting and Decrypting can become very time consuming with Assymetric Encryption
- Solution: Instead of Encrypting the Entire DOC, ***ENcrypt just the HASH of the DOC***
- To check if the Signature is Valid - Produce the `Hash` yourself of the obtained document and decrypt the `encrypted-Hash` obtained from the Server and check if both are EQUAL.

## TLS Handshake

- Server sends a Certificate
  - `Certificate` has the `Public Key`, `Signature` issued by Certificate Authority and the `Domain`
- Client generates a Random key for Symmetric Encryption
- Browser encryptes the Random key using the Public key sent by the Server (certificate)
- Sends this encrypted Random key to the Server

> Symmetric Encryption has two benefits: Faster, More efficient, Scales better

- This will only work if the Server `is in possesion of the private key` and de-crypt the `Random key`
- This will establish TLS connection and HTTP protocol will take over

## Mixed Content Issue

- when site is served through TLS but assets are not ( Images , scripts etc.)

## HTTP / 2

- **HTTP / 1 Issues**
  - _Head-Of-Line blocking = when one requet is blocking other requests from completing_
    - _Browser can open 6 parallel connections with the same Server_
    - Each request is a `request-response` pair, and each pair consumes avg `35ms`
  - _Uncompressed Headers_
    - _Sites compress the http doument body to reduce the `req-response` time_
    - _But Request and Response Headers are sent Uncompressed_
    - _Identical Header data is sent back and forth - like cookies, user-agent etc, Avg 800-bytes in Headers_
  - _Security_
    - _HTTP does not have security measures that is when HTTP + TLS combo arrieved_

- **HTTP / 2 Benefits**
  - _Headers are compressed - headers are not plain-text any more_
  - _HOL is solved through Mutiplexing - uses one connection to send streams(requests). Each stream is split up into frames and multiplexed into a Single connection_
    - _If one stream is blocked another stream takes over_
  - _HTTP2 has a new compression technique tailored towards compressing Headers and Multiplexing_
  - _All streams share the compressor - compressor recognizes same headers appearing in different requests - rather than compressing the same header and sending it, compressor sends a ref for the header instead_

## HTTP / 2 Transition

- No need to bundle JS
- More requests means less Header Data since repeated `Header` value will be replaced by a `ref` and will save time on compression

## Security

### Same-Origin Policy

- Thumb rule - Javascript is not allowed to access data from any `other origin`
- `Origin` is made of three things = `Data Scheme` + `Hostname` + `Port`
- ***`DataScheme: 'https://' ,  HostName : 'WWW.udacity.com' , Port : ':443'`***
- Cross-Origin `fetch` requests are not allowed / or cann't read the response recieved
- **Cannot inspect Iframes / Windows using JS if they are from different Origins**
- _Exceptions - Image Tags, Scripts, Othe CDN resources - BUT cannot inspect their content using Javascript_
- **It is the clients not the servers which enforce Same-Origin Policy**

### Overriding Same Origin Policy

- CORS @ Cross Origin Resource Sharing - 
  - _JSONP - Call the resource with the target function name as one of the query parameteres and server will return a new script callling the function you sent in the query / depends on the fact that both scripts share the same execution environment / used script tags and src url had the query parameter_
  - _CORS headers - allow cross origin request_
    - _Request has the header - `Referer` whose value is the  `HostName`_
    - _Response should have the header - `Access-Control-Allow-Origin` whose value should be the `HostName` provided in the `Referer` above_
    - _To ascertain if CORS request is allowed - A preflight request is made with `OPTIONS` verb  and the response headers are examined for the presence of `Access-Control-Allow-Origin`_

    > NOTE: CORS requests made for Image tags and Forms will not be preflighted / and JS wouldn't be able to access response

### Cross Site Request Frogery (CSRF)

- CSRF token is an additional field appended to the form by the Server and Stored at the server side as well
- IF someone submits a request / a POST request seems to be coming from a `form`, what server does is compares the CSRF token
- Any malacious attempt to create the same form will not have the matching CSRF token

### CROSS SITE SCRIPTING (XSS)

- JS can be injected to another site and gets executed and will access to all the data
- _Happens when User's input si not validated_
- _Places like comment boards - One users leaves a comment - with a hidden script tag - another user in his machine by chance clicks on it or it gets executed when page is loaded with comment - JS inlined with the comment - and kaboom!_

## FINITO !! :D
