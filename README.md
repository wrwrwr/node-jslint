## node-jslint

Easily use [JSLint][] from the command line.

    jslint bin/jslint.js

## What's New

Added latest jslint, 2014-04-21.

Version 0.4.0 of node-jslint exposes a stream interface to jslint.

Version 0.3.4 of node-jslint supports globbing with * and ** expressions.

Versions 0.2+ of node-jslint provide multiple editions of jslint to
address backwards and forwards compatibility.

## Use the command-line client

### Use the default jslint

    jslint lib/color.js

### Always use the latest jslint

    jslint --edition=latest lib/color.js

### Use a specific edition

For example, edition 2013-02-03 which shipped with node-jslint 0.1.9:

    jslint --edition=2013-02-03 lib/color.js

## Use node-jslint programmatically

### Streams interface

As of node-jslint 0.4.0, a streams interface is exposed.  You can use it in client code like this:

   var LintStream = require('jslint').LintStream;

   // configuring the linter
   var l = new LintStream(options);          // can pass jslint options here, including edition
       fileName = 'lib/main.js',
       fileContents = ...;

   // sending files to the linter:
   l.write({file: fileName, body: fileContents});

   // receiving information from the linter
   l.on('data', function (chunk, encoding, callback) {
       // chunk is an object

       // chunk.file is whatever you supplied to write (see above)
       assert.deepEqual(chunk.file, fileName);

       // chunk.linted is an object holding the result from running JSLint
       // chunk.linted.ok is the boolean return code from JSLINT()
       // chunk.linted.errors is the array of errors, etc.
       // see JSLINT for the complete contents of the object

       callback();
   });

You can only pass options to the LintStream when creating it.  The `edition` option can be
used to select different editions of JSLint.

The LintStream is in object mode (objectMode: true).  It expects an
object with two properties: `file` and `body`.  The `file` property
can be used to pass metadata along with the file.  The `body` property
contains the file to be linted; it can be either a string or a Buffer.

The LintStream emits `'data'` events containing an object with two properties.
The `file` property is copied from the `file` property that is passed in.  The
`linted` property contains the results of running JSLINT.

### Simple interface

The simple interface provides an edition-aware loader.  This can be used as a frontend to
node-jslint's collection of editions of the JSLINT code.

    var node_jslint = require('jslint'),
        JSLINT = jslint.load(edition);

This exposes the same loading interface used in node-jslint, so it supports the special
edition names `default` and `latest` as well as date-based edition names such as `2013-08-26`


## Usage examples

Multiple files

    jslint lib/color.js lib/reporter.js

All JSLint options supported

    jslint --white --vars --regexp lib/color.js

Defaults to true, but you can specify false

    jslint --bitwise false lib/color.js

Pass arrays

    jslint --predef $ --predef Backbone lib/color.js

JSLint your entire project

    jslint '**/*.js'

Using JSLint with a config file

Start with the included jslintrc.example file and customize your options
per project or copy it to $HOME/.jslintrc to apply your setting globally

## License

See LICENSE file.

[JSLint]: http://jslint.com/
