## About

This is a XML to JavaScript object parser. It uses the [libxmljs](https://github.com/polotek/libxmljs) module for the actual XML parsing. It aims to be an easy [xml2js](https://github.com/Leonidas-from-XIV/node-xml2js) replacement, but it doesn't follow the xml2js API. I used xml2js for my own needs, but the error reporting of the underlying SAX parser is quite broken. This is how libxml-to-js saw the day light.

libxml-to-js uses the string parser method of libxmljs. Basically a modified version of the algorithm from [here](http://mscdex.net/code-snippets/) in order to fit the formal specifications of xml2js output.

## Installation

Either manually clone this repository into your node_modules directory, or the recommended method:

> npm install libxml-to-js

## Usage mode

```javascript
var parser = require('libxml-to-js');
var xml = 'xml string';

parser(xml, function (error, result) {
	if (error) {
		console.error(error);
	} else {
		console.log(result);
	}
});
```

## Gotcha

Due to the fact that libxmljs does not have any method for returning the namespace attributes of a specific element, the returned namespaces aren't returned into an obvious way:

 * the returned namespaces are only the actual used namespaces by the XML document. If there are unused namespaces, they aren't returned. This is a consequence of the fact that the namespaces are pushed into the returned object as they are detected by the parsing recursion.
 * the returned namespaces are attached as attributes to the root element, into the xmlns key in order to keep the code simple.

Example from the WordPress RSS 2 feed:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0"
	xmlns:content="http://purl.org/rss/1.0/modules/content/"
	xmlns:wfw="http://wellformedweb.org/CommentAPI/"
	xmlns:dc="http://purl.org/dc/elements/1.1/"
	xmlns:atom="http://www.w3.org/2005/Atom"
	xmlns:sy="http://purl.org/rss/1.0/modules/syndication/"
	xmlns:slash="http://purl.org/rss/1.0/modules/slash/"
	>
<!-- the rest of the doc -->
</rss>
```

is parsed as:

```javascript
{ '@': 
   { version: '2.0',
     xmlns: 
      { atom: 'http://www.w3.org/2005/Atom',
        sy: 'http://purl.org/rss/1.0/modules/syndication/',
        dc: 'http://purl.org/dc/elements/1.1/',
        content: 'http://purl.org/rss/1.0/modules/content/',
        wfw: 'http://wellformedweb.org/CommentAPI/',
        slash: 'http://purl.org/rss/1.0/modules/slash/' } },
// the rest of the doc
}
```
