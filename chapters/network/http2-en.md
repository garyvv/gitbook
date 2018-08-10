[HTTP/2 for a Faster Web](https://cascadingmedia.com/insites/2015/03/http-2.html)
=======================================================================

HTTP/2 Objectives
-----------------

The Internet Engineering Task Force (IETF) approved the proposed HTTP/2 standard in February 2015. HTTP/2 is the first major update to HTTP (Hypertext Transfer Protocol) since HTTP/1.1, which was standardized in 1999. The primary objective of HTTP/2 is to maintain high-level compatibility with HTTP/1.1, while decreasing latency. In other words, avoid breaking the Web while making it faster.

SPDY Origins
------------

Since late 2009 Google has been developing an experimental protocol called SPDY (pronounced speedy). SPDY is a trademark of Google and not an acronym. HTTP/2 was originally based on the SPDY experiment. In fact, many SPDY core developers were involved in the development of HTTP/2. As of February 2015, Google announced support for SPDY would be deprecated in favor of HTTP/2 and then completely withdrawn in 2016.

HTTP/1.1
--------

While HTTP/1.1 has served us well since 1999, it was designed for computers and the Web of that time. Needless to say, HTTP is long overdue for an update. To help describe how HTTP/1 works, I have put together the following diagram. Follow the numbers and you will see it starts with the client (possibly a web browser) establishing an HTTP/1 connection to the server on the right.

(2) Client/browser then sends a GET request (HTTP method) for the site's `index.html` page.  
(3) Represents the sever responding with the requested resource.  
(4-7) For our simple example, this request-response cycle continues for the style sheets and scripts in support of this HTML document.  
(8) Finally, the HTTP/1 connection is closed.

![HTTP/1 request/response cycles](../../asset/image/http1-1e8d6f2a.png)

Head-of-Line Blocking
---------------------

As you can see, the client/browser spends a great deal of time waiting on each resource. Because HTTP/1 cannot make concurrent requests over a single connection, browsers usually attempt to expedite the process by opening multiple connections.

Expensive Connections
---------------------

While multiple connections help, from a computer networking perspective, each is very expensive to open. Due to this expense, modern browsers are limited to a maximum of 6-8 HTTP/1.1 connections. With many websites now requiring 80 or more resources, these limitations create a significant performance bottleneck.

HTTP Pipelining
---------------

HTTP/1.1 attempted to correct this performance bottleneck with a technique called HTTP Pipelining. Unfortunately, it still allowed a single large or slow response to block all others that followed. HTTP Pipelining is not difficult to deploy, it is impossible. No modern browser supports HTTP Pipelining because many intermediaries and servers fail to process it correctly.

HTTP/2 Multiplexing
-------------------

Multiplexing allows multiple request-response messages to be in flight over a single HTTP/2 connection, at the same time. To demonstrate how much more efficient an HTTP/2 connection can be, I prepared a side-by-side comparison to HTTP/1. Even in this simplistic example with only three required resources, note how much faster the web page can begin rendering.

Extrapolate this comparison to a more common situation of 80 required resources compounded by the expense of 6-8 HTTP/1.1 connections and the efficiency of a single HTTP/2 connection becomes clear.

![HTTP/1 compared to HTTP/2](../../asset/image/http1-vs-http2-09a032a2.png)

Additional HTTP/2 Performance Factors
-------------------------------------

In addition to multiplexing, HTTP/2 is binary instead of textual like HTTP/1. Compared to textual protocols, binary protocols are more efficient to parse, more compact “on the wire” and less error-prone.

HTTP/2 also reduces overhead by compressing its headers, whereas HTTP/1 does not.

Server Push
-----------

Server Push is an HTTP/2 mechanism for sending data before clients request it. For example, if a request is made for your home page, the server could respond with the home page plus logo and style sheets because it knows the client will need them all. This is essentially the same as inlining all resources within an HTML document, except pushed resources would be cacheable.

A drawback of Server Push is possible redundancy in cases where the client has already cached the resource(s). This is why I recommend using Server Hints instead.

Server Hints
------------

Server Hints notify clients of resources that will be needed, before clients can discover such resources. The server does not send the entire contents of the resource, but only the URL. Clients can then validate their cache and if needed, formally request the resource. Server Hints are not new to HTTP/2 but worth mentioning here because they do not suffer from the possible redundancy drawback of Server Push, as described above.

Server Hints are implemented using the Link header with HTTP and overlaps with existing link prefetching semantics. For example, an HTTP Link header looks like this:

    Link: <https://example.com/images/large-background.jpg>; rel=prefetch
    

No server-side implementation is required if the HTML document includes a `prefetch` link in the head tag. For example:

    <link rel="prefetch" href="https://example.com/images/large-background.jpg">
    

To learn more about `rel="prefetch"`, see [Link prefetching FAQ](https://developer.mozilla.org/en-US/docs/Web/HTTP/Link_prefetching_FAQ) by Mozilla.

Going Further with Resource Hints
---------------------------------

The `preload` relationship is used to declare a resource and its fetch properties. This specification extends functionality with additional processing policies that enable efficient fetching of resources that might be required by the next navigation. For example:

    <!-- fetch and preprocess for next navigation -->
    <link rel="preload" href="//example.com/next-page.html" as="html" loadpolicy="next">
    
    <!-- fetch and do not preprocess for next navigation -->
    <link rel="preload" href="//example.com/next-component.html" as="html" loadpolicy="next inert">
    

The `"next inert"` loadpolicy is equivalent to `rel=prefetch` implemented by some browsers. The `"next"` loadpolicy is semantically equivalent to `rel=prerender` implemented by some browsers. This specification standardizes and extends previous `prefetch` and `prerender` functionality with additional capabilities.

To learn more, see [Resource Hints](https://w3c.github.io/resource-hints/) edited by Ilya Grigorik and published by the W3C.

HTTP/2 Security Criticisms
--------------------------

Although the primary objective of HTTP/2 is to make the Web faster, it has been heavily criticized for not mandating encrypted connections. However, the leading browser makers have thus far refused to implement HTTP/2 without encryption. So by proxy, HTTP/2 will require an encrypted connection to deploy. Please see my article, [HTTPS Everywhere](/insites/2015/01/https-everywhere.html) if you do not agree this is good for the future of the Web.

Browser Support
---------------

HTTP/2 is or will be supported by all major browsers.

*   **Chrome** 40 supports HTTP/2 draft-14, but it is not enabled by default. HTTP/2 draft-17 (final) is available in Chrome Canary 43 (developer pre-release version). Currently, only HTTP/2 over TLS (encrypted) is implemented.  
    To enable HTTP/2 in Chrome, navigate to:
    
        chrome://flags/#enable-spdy4
    
*   **Firefox** supports HTTP/2, which has been enabled by default since version 36. Experimental support for HTTP/2 was originally added in version 34. Currently, only HTTP/2 over TLS is implemented.
*   **Internet Explorer 11** supports HTTP/2 but only in Windows 10 beta, where it is enabled by default. Currently, only HTTP/2 over TLS is implemented.
*   **Spartan** is expected to support HTTP/2 over TLS, although details are still emerging about Microsoft's new browser for Windows 10.
*   **Safari** supports SPDY by default in Mac OS X Yosemite (10.10) and iOS 8. Full HTTP/2 support is expected by the end of 2015.
*   **Opera** supports SPDY by default. Full HTTP/2 support is expected once the final HTTP/2 draft becomes available in Chrome by default.

Server Support
--------------

### HTTP/2 Support

*   **IIS** (Internet Information Services) supports HTTP/2 in Windows 10 beta.
*   **OpenLiteSpeed** 1.3.8 and 1.4.5 support HTTP/2 draft 17.

### SPDY, but no HTTP/2

*   **Apache** provided support for old versions of SPDY via the mod_spdy module but development of this module has stopped.
*   **LiteSpeed Web Server** currently supports SPDY/3.1.
*   **Nginx** provides experimental support for SPDY (Draft 3.1) via a module and plans to support HTTP/2 by the end of 2015.

### HTTP/2 Not Planned

*   **lighttpd** has no support for SPDY or HTTP/2 planned in version 1.x.

### Other HTTP/2 Implementations

Other known implementations of HTTP/2 can be found on the [GitHub HTTP/2 wiki](https://github.com/http2/http2-spec/wiki/Implementations).

Closing HTTP/2 Thoughts
-----------------------

As we have explored, HTTP/2 is a long overdue update for the Web. As it becomes widely adopted over the next few years, websites and other web services will become faster and more capable than ever before. Thanks to forward-thinking browser makers, HTTP/2 will also enhance user privacy and security. While there is more that could be done, I feel that HTTP/2 is a significant step forward for the entire Web.

If you have any questions or comments about HTTP/2, I can be reached on Twitter [@BenjaminPatch](https://twitter.com/BenjaminPatch).

Acknowledgments
---------------

I would like to thank [Ilya Grigorik](https://www.igvita.com/), a web performance engineer at Google, for his helpful contributions to this HTTP/2 article. Ilya is also the author of _High-Performance Browser Networking_, which is a great resource for web developers to learn more about networking and browser performance.

[← InSites](/insites/)