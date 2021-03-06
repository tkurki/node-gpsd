# node-gpsd

Interface to [gpsd](http://www.catb.org/gpsd/).

## Installation

With package manager [npm](http://npmjs.org/):

	npm install node-gpsd

## Code instructions

Require `node-gpsd` by calling:

    var gpsd = require('node-gpsd');
    
`node-gpsd` has 2 classes: `Daemon` and `Listener`.

The `Daemon` is a wrapper to start and stop `gpsd` from your program. The `Listener` interfaces with a running `gpsd` (not necessarily instantiated via the `Daemon` class).

#### Deamon

A `Daemon` is instantiated by calling:

	var daemon = new gpsd.Daemon({
        program: 'gpsd',
    	device: '/dev/ttyUSB0',
    	port: 2947,
    	pid: '/tmp/gpsd.pid',
    	logger: { 
            info: function() {}, 
            warn: console.warn, 
            error: console.error 
        }
	});

The options that are listed above are the default values so calling `new gpsd.Daemon()` will have the same effect. Change the options according your own setup.

The `Daemon` can be started and stopped by calling the appropriate methods:

	daemon.start(function() {
		console.log('Started');
	});

or:

	daemon.stop(function() {
		console.log('Stopped');
	});

The `Daemon` can log to the console if needed. Logging can be controlled by passing a `logger` property in the options when creating the `Daemon` or by setting the logger field:

	daemon.logger = new (winston.Logger) ({ exitOnError: false });

The `Daemon` is an [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter) and will emit the following events:

* `died`: when the `Daemon` is killed.

#### Listener

A `Listener` is instantiated by calling:

	var listener = new gpsd.Listener({
    	port: 2947,
    	hostname: 'localhost',
        logger:  { 
            info: function() {}, 
            warn: console.warn, 
            error: console.error 
        }
	});

The options that are listed above are the default values so calling `new gpsd.Listener()` will have the same effect. Change the options according your own setup.

The `Listener` can be connected to the `gpsd` by calling:

	listener.connect(function() {
		console.log('Connected');
	});

and disconnected by calling:

	listener.disconnect(function() {
		console.log('Disconnected');
	});

The connection state can be queries by calling:

	listener.isConnected();
	
To control watching gps events call the methods:

	listener.watch();
	listener.unwatch();
	
This will put the `Listener` in and out-of watching mode. The `Listener` is an [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter) and will emit the following events:

* `gpsd` events like described in the [gpsd documentation](http://www.catb.org/gpsd/gpsd_json.html). All `gpsd` events like: `TPV`, `SKY`, `INFO` and `DEVICE` can be emitted. To receive all `TPV` events just add `listener.on('TPV', function(tpvData))` to your code.
* `error` when data in a bad format is received from `gpsd`.
* `disconnected` when the connection with `gpsd` is lost.
* `connected` when the connection with `gpsd` is established.
* `error.connection` when the connection is refused.
* `error.socket` on other connection errors.

It is possible to query the gps device by calling:

	listener.version(); /* a INFO event will be emitted */
	listener.devices(); /* a DEVICES event will be emitted */
	listener.device(); /* a DEVICE event will be emitted */
	
The `Listener` can log to the console if needed. Logging can be controlled by passing a `logger` property in the options when creating the `Listener` or by setting the logger field:

	listener.logger = new (winston.Logger) ({ exitOnError: false });;

## Shout outs

Shout outs go to [Pascal Deschenes](http://github.com/pdeschen) for creating the [Bancroft](http://github.com/pdeschen/bancroft) project that formed the basis for `node-gpsd`.
