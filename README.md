LevelGraph-N3
===========

![Logo](https://github.com/mcollina/node-levelgraph/raw/master/logo.png)

[![Build Status](https://travis-ci.org/mcollina/levelgraph-n3.png)](https://travis-ci.org/mcollina/levelgraph-n3)
[![Coverage Status](https://coveralls.io/repos/mcollina/levelgraph-n3/badge.png)](https://coveralls.io/r/mcollina/levelgraph-n3)
[![Dependency Status](https://david-dm.org/mcollina/levelgraph-n3.png?theme=shields.io)](https://david-dm.org/mcollina/levelgraph-n3)
[![Sauce Labs
Tests](https://saucelabs.com/browser-matrix/levelgraph-n3.svg)](https://saucelabs.com/u/levelgraph-n3)

__LevelGraph-N3__ is a plugin for
[LevelGraph](http://github.com/mcollina/levelgraph) that adds the
ability to store, fetch and process N3 and turtle files.

## Install 

### Node.js

Adding support for N3 to LevelGraph is easy:
```shell
$ npm install levelgraph levelgraph-n3 --save
```
Then in your code:
```js
var levelgraph = require('levelgraph'),
    levelgraphN3 = require('levelgraph-n3'),
    db = levelgraphN3(levelgraph('yourdb'));
```


At the moment it requires node v0.10.x, but the port to node v0.8.x
should be straighforward.
If you need it, just open a pull request.

### Browser

If you use [browserify](http://browserify.org/) you can use this package
in a browser just as in node.js. Please also take a look at [Browserify
section in LevelGraph package](https://github.com/mcollina/levelgraph#browserify)

You can also use standalone browserified version from `./build`
directory or use [bower](http://bower.io)

```shell
$ bower install levelgraph-n3 --save
```
It will also install its dependency levelgraph! Now you can simply:

```html
<script src="bower_components/levelgraph/build/levelgraph.js"></script>
<script src="bower_components/levelgraph-n3/build/levelgraph-n3.js"></script>
<script>
  var db = levelgraphN3(levelgraph('yourdb'));
</script>
```

## Usage

We assume in following examples that you created database as explained
above!
```js
var db = levelgraphN3(levelgraph("yourdb"));
```

### Importing n3 files

```js
var fs = require("fs");

var stream = fs.createReadStream("./triples.n3")
               .pipe(db.n3.putStream());

stream.on("finish", function() {
  console.log("Import completed");
});
```

### Get and Put

Storing an N3 file in the database is extremey easy:
```js
var turtle = "@prefix c: <http://example.org/cartoons#>.\n" +
             "c:Tom a c:Cat.\n" +
             "c:Jerry a c:Mouse;\n" +
             "        c:smarterThan c:Tom;\n" +
             "        c:place \"fantasy\".";

db.n3.put(turtle, function(err) {
  // do something after the triple is inserted
});
```

Retrieving it through pattern-matching is extremely easy:
```js
db.n3.get({ subject: "http://example.org/cartoons#Tom" }, function(err, turtle) {
  // turtle is "<http://example.org/cartoons#Tom> a <http://example.org/cartoons#Cat> .\n";
});
```

It even support a Stream interface:
```js
var stream = db.n3.getStream({ subject: "http://example.org/cartoons#Tom" });
stream.on("data", function(data) {
  // data is "<http://example.org/cartoons#Tom> a <http://example.org/cartoons#Cat> .\n";
});
stream.on("end", done);
```

### Exporting NTriples from LevelGraph

__LevelGraph-N3__ allows to export ntriples from a __LevelGraph__ database.
__LevelGraph-N3__ augments the a standard `search` method with a `{ n3: ... }` option
that specifies the subject, predicate and object of the created triples.
It follows the same structure of the `{ materialized: ... }` option (see https://github.com/mcollina/levelgraph#searches).

Here is an example:
```js
db.search([{
  subject: db.v("s"),
  predicate: "http://example.org/cartoons#smarterThan",
  object: db.v("o")
}], { 
  n3: {
    subject: db.v("o"),
    predicate: "http://example.org/cartoons#dumberThan",
    object: db.v("s")
  }
}, function(err, turtle) {
  // turtle is "<http://example.org/cartoons#Tom> <http://example.org/cartoons#dumberThan> <http://example.org/cartoons#Jerry> .\n"
});
```

It also supported by the `searchStream` method.

## Contributing to LevelGraph-N3

* Check out the latest master to make sure the feature hasn't been
  implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't
  requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it
  in a future version unintentionally.
* Please try not to mess with the Makefile and package.json. If you
  want to have your own version, or is otherwise necessary, that is
  fine, but please isolate to its own commit so I can cherry-pick around
  it.

## LICENSE - "MIT License"

Copyright (c) 2013 Matteo Collina (http://matteocollina.com)

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
