# Overall web technologies overview

## Diffie-Hellman
So there are Alice and Bob.

g (small prime number), n (4000 bits long, very big for security reasons) - public numbers.

a - Alice's private number, b - Bob's private number.

1. Alice calculates `g ** a % n`, Bob calculates `g ** b % n` and than they share it.
2. Alice calculates `g ** b % n` to the power of `a`, Bob calculates `g ** a % n` to the power of `b`.
3. They've got the same numbers. `(g ** b ** a) % n == (g ** a ** b) % n`.

Diffie-Hellman algorithm is vunerable for MIMT.

Today Diffie-Hellman is used not with mod. The calculated result e.g. `g ** a` is projected on an eliptic curve, since it's faster. 

## Asymmetric encription

Bob has public and private key. Private key can't be calculated from public one.

To make Alice sure his identity Bob encrypts a message with his private key (makes signature) and send it along with the message. Alice decrypts the signature with Bob's public key and compare the result with the original message. If contents are the same than the message has been sended by Bob and not modified.

We still need Diffie-Hellman for number of reasons:
1. RSA key are established for a long period of time. So if somebody gets it, he can decrypt all of messages.
2. Symmetric encryption is faster.


## Web optimisation


### Critical Render Path
1. DOM
  - JS loading and execution pause HTML parsing
2. CSSOM
3. Render tree
4. Layout
5. Paint

### Tools
1. Webpack
2. Parsel - zero configuration
3. Rollup.js

[Webpack4 configurator ](https://createapp.dev/webpack)

#### Additional tools
- Babel - translates JS code to ES5
- Eslint - syntacs checking


### Optimisation notes

1. Client side
  - HTML
  	- Load CSS at top
  	- Load JS at bottom
  - CSS 
    - Media queries
    - Above the fold loading (load first what you see first)
    - Less specifisity (less files, easier to build CSSOM)
  - JS
    - Load scripts async
    - Deffer scripts loading
    - Minimize DOM manipulation
    - Avoid long running JS

2. Delivery
  - Image compression
  - Code uglification
  - Less files
  - Prefetching, preloading, prebrowsing
  - Code splitting
3. Back-end
  - HTTP/2

