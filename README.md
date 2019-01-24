# Table Parser

A parser to parse table style output from shell

__This project was forked to slim down the prod dependencies__

## Install

You can include it your package.json file as follows:

```
"table-parser": "nicolaspearson/table-parser"
```

## Usage

We have some kind of log below as `test/ps.log`:

```bash
  PID TTY           TIME CMD
49692 ttys000    0:00.06 login -pfl local /bin/bash -c exec -la bash /bin/bash
49693 ttys000    0:00.06 -bash
54195 ttys000    0:00.09 node run

...

56266 ttys005    0:00.06 login -pfl local /bin/bash -c exec -la bash /bin/bash
56269 ttys005    0:00.04 -bash
56463 ttys005    0:00.09 node test.js
56464 ttys005    0:00.01 ps -a
```

Use table-parser to parse it into object:

```
var fs = require( 'fs' );
var Parser = require('table-parser');

var linux_ps = './ps.log';

data = fs.readFileSync( linux_ps ).toString();
var parsedData = Parser.parse( data );

console.log( parsedData );
```

Which will output:

```bash
[ { PID: [ '49692' ],
    TTY: [ 'ttys000' ],
    TIME: [ '0:00.06' ],
    CMD:
     [ 'login',
       '-pfl',
       'local',
       '/bin/bash',
       '-c',
       'exec',
       '-la',
       'bash',
       '/bin/bash' ] },
  ...

  { PID: [ '56464' ],
    TTY: [ 'ttys005' ],
    TIME: [ '0:00.01' ],
    CMD: [ 'ps', '-a' ] } ]
```

## Double quotation marks

Normally, all the values will be transformed into array using `split( /\s+/ )`, but string wrapped with `"` will be treated as a continuous string.

For example, the CommandLine below:

```
"C:\Program Files\Internet Explorer\iexplore.exe" --name="Local" --type=parse
```

will be split into:

- `C:\Program Files\Internet Explorer\iexplore.exe` ( `"` will be removed, if `"` is at the beginning )
- `--name="Local"`    ( `"` is reserved )
- `--type=parse`
