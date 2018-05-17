---
layout: post
title: Mitmproxy Cheat Sheet
date: 2017-02-10 17:33:00.000000000 +01:00
type: post
published: true
status: publish
categories:
- howto
tags:
- mitmproxy
- command
- filter
- keyboard
- interactive
meta:
author: Martin Stut
---
When starting to use [Mitmproxy](https://mitmproxy.org/) (see also [my blog post about setting it up](https://www.stut-it.net/blog/2017/watching-windows-internet-traffic-mitmproxy.html)), you need to remember a significant number of details. To help you in this, I have compiled frequently used items in "cheat sheet" style - no background explanation, but a concise list of vocabulary. Much of this is copied, selected and rearranged from http://docs.mitmproxy.org/ . In the first sections, I'm only listing what I regularly need, while the Appendices contain the full data.

## Command Line Options

### Optional Arguments

| Argument        | Effect                                   |
| :-------------- | :--------------------------------------- |
| `--help`        | Show this help message and exit.         |
| `--anticache`   | Strip out request headers that might cause the server to return 304-not-modified. |
| `-r RFILE`      | Read flows from file.                    |
| `-w STREAMFILE` | Write flows to file.                     |
| `-a STREAMFILE` | Append flows to file.                    |
| `--anticomp`    | Try to convince servers to send us un-compressed data. |
| `-n`            | Don't start a proxy server. Useful for offline analyzing a previously captured stream. |
| `-f FILTER`     | Filter view expression.                  |

## Interactive Keyboard Commands

### Global Keys (any View)

| Key  | Function                                 |
| :--- | :--------------------------------------- |
| `?`  | show context sensitive help (very useful) |
| `q`  | quit / return to previous page           |
| `O`  | Options                                  |

### Movement (most Views)

| Key                       | Function              |
| :------------------------ | :-------------------- |
| `g`, `G`                  | go to beginning, end  |
| `pg up`/`down` or `space` | page up/down          |
| arrows                    | up, down, left, right |

### Flow List (initial view)

| Key     | Function                                 |
| :------ | :--------------------------------------- |
| `enter` | view flow (go to Flow View)              |
| `e`     | toggle eventlog                          |
| `tab`   | tab between eventlog and flow list       |
| `o`     | set flow order, will ask for sort criteria - useful for finding and deleting large download flows before saving stream |
| `d`     | delete flow                              |
| `f`     | filter view                              |
| `w`     | save flows                               |
| `z`     | zap (clear) flow list or eventlog        |
| `q`     | quit mitmproxy                           |

### Flow View (single flow)

| Key     | Effect                                   |
| :------ | :--------------------------------------- |
| `tab`   | next tab (cycles through Request - Response - Detail) |
| `w`     | save all flows matching current view filter |
| `f`     | load full body data                      |
| `space` | next flow                                |
| `/`     | search (case sensitive)                  |
| `n`     | repeat search forward                    |
| `N`     | repeat search backwards                  |
| `q`     | return to flow list                      |

## Filter Expressions

| Expression  | Description                    |
| :---------- | :----------------------------- |
| `~b regex`  | Body                           |
| `~c int`    | HTTP response code             |
| `~d regex`  | Domain                         |
| `~e`        | Match error                    |
| `~h regex`  | Header                         |
| `~hq regex` | Request header                 |
| `~m regex`  | Method                         |
| `~q`        | Match request with no response |
| `~s`        | Match response                 |
| `~t regex`  | Content-type header            |
| `~u regex`  | URL                            |
| `!`         | unary not                      |
| `&`         | and                            |
| `\|`        | or                             |
| `(...)`     | grouping                       |

### Examples

URLs containing "lastpass": `lastpass`

Exclude flows connecting to google and doubleclick: `(! google) & (! doubleclick)`

All requests sending cookies: `~hq Cookie`

## Appendix 1: Full List of Command Line Options

This is essentially the output of `mitmproxy --help`, formatted for better web page reading.



usage: `mitmproxy [options]`

### Optional Arguments

| Argument                                 | Effect                                   |
| :--------------------------------------- | :--------------------------------------- |
| `-h,` `--help`                           | show this help message and exit          |
| `--conf PATH`                            | Configuration file                       |
| `--version`                              | show program's version number and exit   |
| `--sysinfo`,  `--shortversion`           | show program's short version number and exit |
| `--anticache`                            | Strip out request headers that might cause the server to return 304-not-modified |
| `--cadir CADIR`                          | Location of the default mitmproxy CA files. (`~/.mitmproxy`) |
| `--host`                                 | Use the Host header to construct URLs for display |
| `-q`, `--quiet`                          | Quiet                                    |
| `-r RFILE`, `--read-flows RFILE`         | Read flows from file.                    |
| `-s "script.py --bar"`, `--script "script.py --bar"` | Run a script. Surround with quotes to pass script arguments. Can be passed multiple times. |
| `-t FILTER`, `--stickycookie FILTER`     | Set sticky cookie filter. Matched against requests. |
| `-u FILTER`, `--stickyauth FILTER`       | Set sticky auth filter. Matched against requests. |
| `-v`, `--verbose`                        | Increase log verbosity.                  |
| `-w STREAMFILE`, `--wfile STREAMFILE`    | Write flows to file.                     |
| `-a STREAMFILE`, `--afile STREAMFILE`    | Append flows to file.                    |
| `-z`, `--anticomp`                       | Try to convince servers to send us un-compressed data. |
| `-Z SIZE`, `--body-size-limit SIZE`      | Byte size limit of HTTP request and response bodies. Understands k/m/g suffixes, i.e. 3m for 3 megabytes. |
| `--stream SIZE`                          | Stream data to the client if response body exceeds the given threshold. If streamed, the body will not be stored in any way. Understands k/m/g suffixes, i.e. 3m for 3 megabytes. |
| `--upstream-auth UPSTREAM_AUTH`          | Add HTTP Basic authentcation to upstream proxy and reverse proxy requests. Format: `username:password` |
| `--palette {dark,light,lowdark,lowlight,solarized_dark,solarized_light}` | Select color palette: lowlight, light, lowdark, solarized_light, solarized_dark, dark |
| `--palette-transparent`                  | Set transparent background for palette.  |
| `-e`, `--eventlog`                       | Show event log.                          |
| `--follow`                               | Focus follows new flows.                 |
| `--order {time,method,url,size}`         | Flow sort order.                         |
| `--no-mouse`                             | Disable mouse interaction.               |

### Proxy Modes

| Argument                                 | Effect                                   |
| :--------------------------------------- | :--------------------------------------- |
| `-R REVERSE_PROXY`, `--reverse REVERSE_PROXY` | Forward all requests to upstream HTTP server:  `http[s]://host[:port]`. Clients can always connect both via HTTPS and HTTP, the connection to the server is determined by the specified scheme. |
| `--socks`                                | Set SOCKS5 proxy mode.                   |
| `-T`, `--transparent`                    | Set transparent proxy mode.              |
| `-U UPSTREAM_PROXY`, `--upstream UPSTREAM_PROXY` | Forward all requests to upstream proxy server: http://host[:port] |

### Proxy Options

| Option                                   | Effect                                   |
| :--------------------------------------- | :--------------------------------------- |
| `-b ADDR`, `--bind-address ADDR`         | Address to bind proxy to (defaults to all interfaces) |
| `-I HOST`, `--ignore HOST`               | Ignore host and forward all traffic without processing it. In transparent mode, it is recommended to use an IP address (range), not the hostname. In regular mode, only SSL traffic is ignored and the hostname should be used. The supplied value is interpreted as a regular expression and matched on the ip or the hostname. Can be passed multiple times. |
| `--tcp HOST`                             | Generic TCP SSL proxy mode for all hosts that match the pattern. Similar to `--ignore`, but SSL connections are intercepted. The communication contents are printed to the log in verbose mode. |
| `-n`, `--no-server`                      | Don't start a proxy server. Useful for offline analyzing a previously captured stream. |
| `-p PORT`, `--port PORT`                 | Proxy service port. Default: 8080        |
| `--http2`,  `--no-http2`                 | Explicitly enable/disable HTTP/2 support. Disabled by default until major websites implement the spec correctly. Default value will change in a future version. |
| `--no-websocket`, `--websocket`          | Explicitly enable/disable WebSocket support. Enabled by default. |
| `--raw-tcp`, `--no-raw-tcp`              | Explicitly enable/disable experimental raw tcp support. Disabled by default. Default value will change in a future version. |
| `--spoof-source-address`                 | Use the client's IP for server-side connections. Combine with --upstream-bind-address to spoof a fixed source address. |
| `--upstream-bind-address UPSTREAM_BIND_ADDRESS` | Address to bind upstream requests to (defaults to none) |



### SSL

| Option                                   | Effect                                   |
| :--------------------------------------- | :--------------------------------------- |
| `--cert SPEC`                            | Add an SSL certificate. SPEC is of the form `[domain=]path`. The domain may include a wildcard, and is equal to `*` if not specified. The file at path is a certificate in PEM format. If a private key is included in the PEM, it is used, else the default key in the conf dir is used. The PEM file should contain the full certificate chain, with the leaf certificate as the first entry. Can be passed multiple times. |
| `--ciphers-client CIPHERS_CLIENT`        | Set supported ciphers for client connections. (OpenSSL Syntax) |
| `--ciphers-server CIPHERS_SERVER`        | Set supported ciphers for server connections. (OpenSSL Syntax) |
| `--client-certs CLIENTCERTS`             | Client certificate file or directory.    |
| `--no-upstream-cert`                     | Don't connect to upstream server to look up certificate details. |
| `--add-upstream-certs-to-client-chain`   | Add all certificates of the upstream server to the certificate chain that will be served to the proxy client, as extras. |
| `--insecure`                             | Do not verify upstream server SSL/TLS certificates. |
| `--upstream-trusted-cadir SSL_VERIFY_UPSTREAM_TRUSTED_CADIR` | Path to a directory of trusted CA certificates for upstream server verification prepared using the c_rehash tool. |
| `--upstream-trusted-ca SSL_VERIFY_UPSTREAM_TRUSTED_CA` | Path to a PEM formatted trusted CA certificate. |
| `--ssl-version-client {SSLv3,all,TLSv1_1,TLSv1_2,secure,SSLv2,TLSv1}` | Set supported SSL/TLS versions for client connections. SSLv2, SSLv3 and 'all' are INSECURE. Defaults to secure, which is TLS1.0+. |
| `--ssl-version-server {SSLv3,all,TLSv1_1,TLSv1_2,secure,SSLv2,TLSv1}` | Set supported SSL/TLS versions for server connections. SSLv2, SSLv3 and 'all' are INSECURE. Defaults to secure, which is TLS1.0+. |

### Onboarding App

| Option                | Effect                                   |
| :-------------------- | :--------------------------------------- |
| `--noapp`             | Disable the mitmproxy onboarding app.    |
| `--app-host APP_HOST` | Domain to serve the onboarding app from. For transparent mode, use an IP when a DNS entry for the app domain is not present. Default: `mitm.it` |
| `--app-port 80`       | Port to serve the onboarding app from. Does not need to be reachable from outside, because the client will go through the built-in proxy anyway. |

### Client Replay

| Option                            | Effect                                   |
| :-------------------------------- | :--------------------------------------- |
| `-c PATH`, `--client-replay PATH` | Replay client requests from a saved file. |

### Server Replay

| Option                                   | Effect                                   |
| :--------------------------------------- | :--------------------------------------- |
| `-S PATH`, `--server-replay PATH`        | Replay server responses from a saved file. |
| `-k`, `--replay-kill-extra`              | Kill extra requests during replay.       |
| `--server-replay-use-header SERVER_REPLAY_USE_HEADERS` | Request headers to be considered during replay. Can be passed multiple times. |
| `--norefresh`                            | Disable response refresh, which updates times in cookies and headers for replayed responses. |
| `--no-pop`                               | Disable response pop from response flow. This makes it possible to replay same response multiple times. |
| `--replay-ignore-content`                | Ignore request's content while searching for a saved flow to replay |
| `--replay-ignore-payload-param SERVER_REPLAY_IGNORE_PAYLOAD_PARAMS` | Request's payload parameters (application/x-www-form-urlencoded or multipart/form-data) to be ignored while searching for a saved flow to replay. Can be passed multiple times. |
| `--replay-ignore-param SERVER_REPLAY_IGNORE_PARAMS` | Request's parameters to be ignored while searching for a saved flow to replay. Can be passed multiple times. |
| `--replay-ignore-host`                   | Ignore request's destination host while searching for a saved flow to replay |

### Replacements

Replacements are of the form "/pattern/regex/replacement", where the separator can be any character. Please see the documentation for more information.

| Option                     | Effect                                   |
| :------------------------- | :--------------------------------------- |
| `--replace PATTERN`        | Replacement pattern.                     |
| `--replace-from-file PATH` | Replacement pattern, where the replacement clause is a path to a file. |



### Set Headers

Header specifications are of the form "/pattern/header/value", where the separator can be any character. Please see the documentation for more information.

| Option                | Effect              |
| :-------------------- | :------------------ |
| `--setheader PATTERN` | Header set pattern. |



### Proxy Authentication

Specify which users are allowed to access the proxy and the method used for authenticating them.

| Option              | Effect                                   |
| :------------------ | :--------------------------------------- |
| `--nonanonymous`    | Allow access to any user long as a credentials are specified. |
| `--singleuser USER` | Allows access to a a single user, specified in the form `username:password` . |
| `--htpasswd PATH`   | Allow access to users specified in an Apache htpasswd file. |

### Filters

See help in mitmproxy for filter expression syntax.

| Option                                  | Effect                                   |
| :-------------------------------------- | :--------------------------------------- |
| `-i INTERCEPT`, `--intercept INTERCEPT` | Intercept filter expression. "Intercepting" means waiting for user approval/modification before passing on/back to server/client. |
| `-f FILTER`, `--filter FILTER`          | Filter view expression.                  |

## Appendix 2: Full List of Interactive Keyboard Commands

This is essentially a copy of the help screens of mitmproxy, as retrieved by the `?` key.

### Global Keys (any View)

      i  set interception pattern
      O  options
      q  quit / return to previous page
      Q  quit without confirm prompt
      R  replay of requests/responses from file

### Movement (most Views)

      j, k           down, up
      h, l           left, right (in some contexts)
      g, G           go to beginning, end
      space          page down
      pg up/down     page up/down
      ctrl+b/ctrl+f  page up/down
      arrows         up, down, left, right

### Flow List (initial view)

      A      accept all intercepted flows
      a      accept this intercepted flow
      b      save request/response body
      C      export flow to clipboard
      d      delete flow
      D      duplicate flow
      e      toggle eventlog
      E      export flow to file
      f      filter view
      F      toggle follow flow list
      L      load saved flows
      m      toggle flow mark
      M      toggle marked flow view
      n      create a new request
      o      set flow order
      r      replay request
      S      server replay request/s
      U      unmark all marked flows
      v      reverse flow order
      V      revert changes to request
      w      save flows
      W      stream flows to file
      X      kill and delete flow, even if it's mid-intercept
      z      clear flow list or eventlog
      tab    tab between eventlog and flow list
      enter  view flow
      |      run script on this flow

### Flow View (single flow)

      A      accept all intercepted flows
      a      accept this intercepted flow
      b      save request/response body
      C      export flow to clipboard
      D      duplicate flow
      d      delete flow
      e      edit request/response
      f      load full body data
      m      change body display mode for this entity
             (default mode can be changed in the options)
             *a*utomatic: automatic detection
             h*e*x: Hex
             *h*tml: HTML
             *i*mage: Image
             *j*avascript: JavaScript
             j*s*on: JSON
             *u*rlencoded: URL-encoded data
             *r*aw: raw data
             *x*ml: XML
      E      export flow to file
      r      replay request
      V      revert changes to request
      v      view body in external viewer
      w      save all flows matching current view filter
      W      save this flow
      x      delete body
      z      encode/decode a request/response
      tab    next tab (cycles through Request - Response - Detail)
      h, l   previous tab, next tab
      space  next flow
      |      run script on this flow
      /      search (case sensitive)
      n      repeat search forward
      N      repeat search backwards

## Appendix 3: Full List of Filter Expressions

This is essentially a copy of http://docs.mitmproxy.org/en/stable/features/filters.html .

Many commands in **mitmproxy** and **mitmdump** take a filter expression. Filter expressions consist of the following operators:

| Expression   | Description                              |
| :----------- | :--------------------------------------- |
| `~a`         | Match asset in response: CSS, Javascript, Flash, images. |
| `~b regex`   | Body                                     |
| `~bq regex`  | Request body                             |
| `~bs regex`  | Response body                            |
| `~c int`     | HTTP response code                       |
| `~d regex`   | Domain                                   |
| `~dst regex` | Match destination address                |
| `~e`         | Match error                              |
| `~h regex`   | Header                                   |
| `~hq regex`  | Request header                           |
| `~hs regex`  | Response header                          |
| `~http`      | Match HTTP flows                         |
| `~m regex`   | Method                                   |
| `~marked`    | Match marked flows                       |
| `~q`         | Match request with no response           |
| `~s`         | Match response                           |
| `~src regex` | Match source address                     |
| `~t regex`   | Content-type header                      |
| `~tcp`       | Match TCP flows                          |
| `~tq regex`  | Request Content-Type header              |
| `~ts regex`  | Response Content-Type header             |
| `~u regex`   | URL                                      |
| `!`          | unary not                                |
| `&`          | and                                      |
| `\|`         | or                                       |
| `(...)`      | grouping                                 |

- Regexes are Python-style
- Regexes can be specified as quoted strings
- Header matching (`~h`, `~hq`, `~hs`) is against a string of the form “name: value”.
- Strings with no operators are matched against the request URL.
- The default binary operator is `&`.

### Examples

URL containing “google.com”: `google\.com`

Requests whose body contains the string “test”: `~q ~b test`

Anything but requests with a text/html content type: `!(~q & ~t "text/html")`

