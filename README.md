LibXML bindings for Google's v8 javascript engine.

It also plays nicely with [node.js](http://github.com/ry/node)

## Basics
### Links 
* [GitHub](http://github.com/sprsquish/libxmljs)
* [Docs](http://libxmljs.squishtech.com/)

### Requirements

* [libxml](http://xmlsoft.org/)
* [v8](http://code.google.com/apis/v8/intro.html) _(for the standalone version)_
* [node.js](http://github.com/ry/node) _(for the node.js version)_

### Building
To build the node.js plugin use:

    scons libxmljs.node

_Standalone version is coming soon_


## Using
### node.js
Move libxmljs.node to your project's directory and throw this into your script:

    var libxml = require('libxmljs');


## API
### Builder
There are two main ways to use the builder. The first:

    var doc = new libxml.Document(function(n) {
      n.node('root', function(n) {
        n.node('child', {foo: 'bar'}, function(n) {
          n.node('grandchild', {baz: 'fizbuzz'}, 'grandchild content');
        });
        n.node('sibling', 'with content!');
      });
    });

Calling doc.toString() will yield the following XML:

    <?xml version="1.0" encoding="UTF-8"?>
    <root><child foo="bar"><grandchild baz="fizbuzz">grandchild content</grandchild></child><sibling>with content!</sibling></root>

The other way is through chaining (indentation is meant to represent location in the tree):

    var doc = new libxml.Document();
      doc.node('root')
        .node('child', {foo: 'bar'})
          .node('grandchild', {baz: 'fizbuzz'}, 'grandchild content')
        .parent()
      .parent()
        .node('sibling', 'with content!');

This will yield the same results.

### Parsing
By file:
    var doc = libxml.parseFile([filename]);
By String:
    var doc = libxml.parseString([xmlString]);

### SAX Parsing
Callbacks for SAX are created during the instantiation of the parser object.
You can omit any callback you're not interested in. Below is just a list of callbacks currently supported with the arguments they'll be sent.

    var parser = new libxml.SaxParser(function(cb) {
      cb.onStartDocument(function() {});
      cb.onEndDocument(function() {});
      cb.onStartElementNS(function(elem, attrs, prefix, uri, namespaces) {});
      cb.onEndElementNS(function(elem, prefix, uri) {});
      cb.onCharacters(function(chars) {});
      cb.onCdata(function(cdata) {});
      cb.onComment(function(msg) {});
      cb.onWarning(function(msg) {});
      cb.onError(function(msg) {});
    });

    // Parse a string:
    parser.parseString([xmlString]);

    // Parse a file:
    parser.parseFile([filename]);

### SAX Push Parsing
Push parsers are created the same way DOM parsers are, but take input a chunk at a time:

    var parser = new libxml.SaxPushParser(function(cb) {
      cb.onStartDocument(function() {});
      cb.onEndDocument(function() {});
      cb.onStartElementNS(function(elem, attrs, prefix, uri, namespaces) {});
      cb.onEndElementNS(function(elem, prefix, uri) {});
      cb.onCharacters(function(chars) {});
      cb.onCdata(function(cdata) {});
      cb.onComment(function(msg) {});
      cb.onWarning(function(msg) {});
      cb.onError(function(msg) {});
    });

    while(xmlChunk)
      parser.push(xmlChunk);
