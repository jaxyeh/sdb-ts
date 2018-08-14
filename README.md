# SDB-TS
sdb-ts is a TypeScript shim for [ShareDB](https://github.com/share/sharedb).

This project is undergoing sporadic development.

## NPM Install:
To install via npm, run:

```
npm install sdb-ts
```

## Client-side Usage
For client-side code, use [`built/sdb-client-bundle.js`](https://raw.githubusercontent.com/soney/sdb-ts/master/built/sdb-client-bundle.js).

## Usage Examples


### Creating a server (in Node.js):

If no argument is provided, the server will pick a port:
```
import { SDBServer } from '../built/index'; 
async(() => {
	const sdbServer = new SDBServer();
	const { port }  = await sdbServer.address();

	await sdbServer.listening();
	console.log(`Created server on port ${port}`);
});
```

Otherwise, the constructor can be called with the server passed in:
```
import * as WebSocket from 'ws';
import * as http from 'http';
import { SDBServer } from '../built/index'; 

const server = http.createServer(8000);
const wss = new WebSocket.Server({ server });
const sdbServer = new SDBServer(wss);
server.listen();
const address = server.address() as AddressInfo;
```

### Working with Documents

```
import { SDBServer, SDBDoc } from '../built/index'; 
//...

interface CounterDoc {
	counter: number;
	sd: {
		strEx: string;
		numEx: number;
	};
}

const sCounterDoc: SDBDoc<CounterDoc> = sdbServer.get('ex', 'counter');

sCounterDoc.createIfEmpty({
	counter: 0,
	sd: {
		strEx: 'ABC',
		numEx: 3
	}
});
scounterDoc.subscribe((eventType: string, ops, source, data) => {
	if(eventType === null) {
		console.log('subscribed', data);
	} else if(eventType === 'create') {
		console.log('create', data);
	} else if(eventType === 'op') {
		console.log(ops);
	}
	console.log(scounterDoc.getData());
	console.log(sCounterDoc.traverse(['sd', 'numEx']));
});

sCounterDoc.submitNumberAddOp(['counter'], 1);

const subDoc = d.subDoc(['sd']);
subDoc.subscribe();

subDoc.submitObjectReplaceOp(['numEx'], 33);
```