# Repositories

## Documentation

* [realtime-handbook](https://github.com/xwiki-labs/realtime-handbook)

***

## Platforms

### Cryptpad

We use [CryptPad](https://github.com/xwiki-labs/cryptpad) as our platform for rapid prototyping.

It features a growing list of collaborative real time editors:

* plain text
* code editing with highlighting
* CKEditor WYSIWYG
* collaborative forms
* collaborative lists and maps
* whiteboard (via canvas)
* basic code editing with execution button
* markdown rendering (with no input)
* style editing

## Chainpad Server

[Chainpad-Server](https://github.com/xwiki-labs/chainpad-server) is a stripped down version of Cryptpad's server.

It provides a basic webserver, a _Netflux_ server, and a (pluggable) datastore.

***

## Libraries

### Chainpad

The [Chainpad Realtime Collaborative Editor Algorithm](https://github.com/xwiki-contrib/chainpad) implements an engine which employs [Operational Transformation](https://en.wikipedia.org/wiki/Operational_transformation) and a data structure which is based on Nakamoto Blockchains which is used to achieve consensus in a peer to peer fashion.

It forms the core of all of our realtime applications, resolving conflicts and allowing clients to collectively determine the state of a collaborative document.

Chainpad is transport-agnostic, meaning you can bind it to any transport medium, depending on your needs.

### Netflux-Websocket

[netflux-websocket](https://github.com/xwiki-labs/netflux-websocket) is XWiki's implementation of the [Netflux 2.0 specification](https://github.com/xwiki-labs/netflux-spec2).

It acts as the transportation layer which allows several client's Chainpad instances to communicate.

### Chainpad-Netflux

[Chainpad-Netflux](https://github.com/xwiki-labs/chainpad-netflux) bundles Chainpad with the Netflux transport layer, and exposes a simplified API for developing collaborative applications.

It acts as the centerpoint around which all of Cryptpad's prototypes are built.

### TextPatcher

Chainpad treats all documents as text, and formulates changes to documents as sets of insertions and deletions of parts of that text.

[TextPatcher](https://github.com/xwiki-labs/textpatcher) formulates changes to a document in a way that Chainpad can understand.

### Chainpad-Crypto

Chainpad does not rely on any central authority to reconcile conflicts, however, most use cases employ a server for storing and relaying the messages which constitute a document's history.

Since all logic is executed on clients, applications can use symmetric encryption and shared keys to keep the relay server from being able to reproduce the content of the document, even though it has access to all its messages.

[Chainpad-Crypto](https://github.com/xwiki-labs/chainpad-crypto) depends [TweetNaCl.js](https://tweetnacl.js.org/#/) and exports functions for creating and parsing encryption keys, as well as using those keys to encrypt and decrypt messages.

### Hyperjson

We developed [Hyperjson](https://github.com/xwiki-labs/hyperjson) as a browser-independant format for serializing the current state of a DOM subtree.

It is used to represent the state of a document in Cryptpad's Realtime CKEditor WYSIWYG, with [Hyperscript](https://github.com/dominictarr/hyperscript) and [DiffDOM](https://github.com/fiduswriter/diffDOM) being used to convert the format back into a DOM and update a user's live interface with minimal interuptions.

### Chainpad-JSON-Validator

Chainpad uses Operational Transformation to reconcile concurrent changes to a document by remote clients, and allows for pluggable transformation functions to be provided by the application layer.

[Chainpad-json-validator](https://github.com/xwiki-labs/chainpad-json-validator) ensures that the result of a transformation results in valid JSON, allowing clients to patch on complex datatypes instead of plain text.

### Chainpad-Listmap

[The Chainpad List/Map API](https://github.com/xwiki-labs/chainpad-listmap) turns any Javascript object or array into a collaborative datatype.

Changes made to the resulting object will propogate to remote users, who can implement listeners for particular parts of the object by specifying paths within arbitrarily nested objects or arrays.

***

## XWiki Extensions

[XWiki Enterprise](https://xwiki.org) is an extensible knowledge management solution made even more powerful by the realtime extensions we've been developing.

Generally we create prototypes in Cryptpad first, then we implement XWiki extensions, installed with a few clicks via the XWiki extension manager which connects to [extensions.xwiki.org](http://extensions.xwiki.org/xwiki/bin/view/Main/WebHome).

The following plugins are available now, with more to come in the future.

* https://github.com/xwiki-contrib/realtime-wikitext
* https://github.com/xwiki-contrib/realtime-wysiwyg
* https://github.com/xwiki-labs/realtime-frontend
* https://github.com/xwiki-labs/realtime-form
* https://github.com/xwiki-contrib/xwiki-platform-realtime
* https://github.com/xwiki-contrib/realtime-backend

## Inactive

The following repositories are not being actively developed, though their resources may be of use to you.

* https://github.com/xwiki-labs/ChainJSON
* https://github.com/xwiki-labs/RealtimeJSON
* https://github.com/xwiki-labs/netflux
* https://github.com/xwiki-labs/chatflux
* https://github.com/xwiki-labs/otaml
* https://github.com/xwiki-labs/cryptsheet
* https://github.com/xwiki-labs/cryptpad-rjs

