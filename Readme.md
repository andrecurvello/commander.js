# Commander.js

  The complete solution for [node.js](http://nodejs.org) command-line interfaces, inspired by Ruby's [commander](https://github.com/visionmedia/commander).

 [![Build Status](https://secure.travis-ci.org/visionmedia/commander.js.png)](http://travis-ci.org/visionmedia/commander.js)

## Installation

    $ npm install commander

## Option parsing

 Options with commander are defined with the `.option()` method, also serving as documentation for the options. The example below parses args and options from `process.argv`, leaving remaining args as the `program.args` array which were not consumed by options.

In addition, since the program object has a number of other
properties associated with it, the simple
list of options that were parsed from the command line can
be read from program.parsedOptions.

```js
#!/usr/bin/env node

/**
 * Module dependencies.
 */

var program = require('commander');

program
  .version('0.0.1')
  .option('-p, --peppers', 'Add peppers')
  .option('-P, --pineapple', 'Add pineapple')
  .option('-b, --bbq', 'Add bbq sauce')
  .option('-c, --cheese [type]', 'Add the specified type of cheese [marble]', 'marble')
  .parse(process.argv);

console.log('you ordered a pizza with:');
if (program.peppers) console.log('  - peppers');
if (program.pineapple) console.log('  - pineappe');
if (program.bbq) console.log('  - bbq');
//access via .parsedOptions
if (program.parsedOptions['bbq']) console.log('  - bbq');
console.log('  - %s cheese', program.cheese);
```

 Short flags may be passed as a single arg, for example `-abc` is equivalent to `-a -b -c`. Multi-word options such as "--template-engine" are camel-cased, becoming `program.templateEngine` etc.

## Automated --help

 The help information is auto-generated based on the information commander already knows about your program, so the following `--help` info is for free:

```  
 $ ./examples/pizza --help

   Usage: pizza [options]

   Options:

     -V, --version        output the version number
     -p, --peppers        Add peppers
     -P, --pineapple      Add pineappe
     -b, --bbq            Add bbq sauce
     -c, --cheese <type>  Add the specified type of cheese [marble]
     -h, --help           output usage information

```

## Coercion

```js
function range(val) {
  return val.split('..').map(Number);
}

function list(val) {
  return val.split(',');
}

program
  .version('0.0.1')
  .usage('[options] <file ...>')
  .option('-i, --integer <n>', 'An integer argument', parseInt)
  .option('-f, --float <n>', 'A float argument', parseFloat)
  .option('-r, --range <a>..<b>', 'A range', range)
  .option('-l, --list <items>', 'A list', list)
  .option('-o, --optional [value]', 'An optional value')
  .parse(process.argv);

console.log(' int: %j', program.integer);
console.log(' float: %j', program.float);
console.log(' optional: %j', program.optional);
program.range = program.range || [];
console.log(' range: %j..%j', program.range[0], program.range[1]);
console.log(' list: %j', program.list);
console.log(' args: %j', program.args);
```

## Custom help

 You can display arbitrary `-h, --help` information
 by listening for "--help". Commander will automatically
 exit once you are done so that the remainder of your program
 does not execute causing undesired behaviours, for example
 in the following executable "stuff" will not output when
 `--help` is used.

```js
#!/usr/bin/env node

/**
 * Module dependencies.
 */

var program = require('../');

function list(val) {
  return val.split(',').map(Number);
}

program
  .version('0.0.1')
  .option('-f, --foo', 'enable some foo')
  .option('-b, --bar', 'enable some bar')
  .option('-B, --baz', 'enable some baz');

// must be before .parse() since
// node's emit() is immediate

program.on('--help', function(){
  console.log('  Examples:');
  console.log('');
  console.log('    $ custom-help --help');
  console.log('    $ custom-help -h');
  console.log('');
});

program.parse(process.argv);

console.log('stuff');
```

yielding the following help output:

```

Usage: custom-help [options]

Options:

  -h, --help     output usage information
  -V, --version  output the version number
  -f, --foo      enable some foo
  -b, --bar      enable some bar
  -B, --baz      enable some baz

Examples:

  $ custom-help --help
  $ custom-help -h

```

## .prompt(msg, fn)

 Single-line prompt:

```js
program.prompt('name: ', function(name){
  console.log('hi %s', name);
});
```

 Multi-line prompt:

```js
program.prompt('description:', function(name){
  console.log('hi %s', name);
});
```

 Coercion:

```js
program.prompt('Age: ', Number, function(age){
  console.log('age: %j', age);
});
```

```js
program.prompt('Birthdate: ', Date, function(date){
  console.log('date: %s', date);
});
```

## .password(msg[, mask], fn)

Prompt for password without echoing:

```js
program.password('Password: ', function(pass){
  console.log('got "%s"', pass);
  process.stdin.destroy();
});
```

Prompt for password with mask char "*":

```js
program.password('Password: ', '*', function(pass){
  console.log('got "%s"', pass);
  process.stdin.destroy();
});
```

## .confirm(msg, fn)

 Confirm with the given `msg`:

```js
program.confirm('continue? ', function(ok){
  console.log(' got %j', ok);
});
```

## .choose(list, fn)

 Let the user choose from a `list`:

```js
var list = ['tobi', 'loki', 'jane', 'manny', 'luna'];

console.log('Choose the coolest pet:');
program.choose(list, function(i){
  console.log('you chose %d "%s"', i, list[i]);
});
```

## Links

 - [API documentation](http://visionmedia.github.com/commander.js/)
 - [ascii tables](https://github.com/LearnBoost/cli-table)
 - [progress bars](https://github.com/visionmedia/node-progress)
 - [more progress bars](https://github.com/substack/node-multimeter)
 - [examples](https://github.com/visionmedia/commander.js/tree/master/examples)

## License 

(The MIT License)

Copyright (c) 2011 TJ Holowaychuk &lt;tj@vision-media.ca&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
