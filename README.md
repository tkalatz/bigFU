# bigFU
Asynchronous very Big File Upload

This is a node.js stream-based code that allows asynchronous big file (including very big) upload with bytes monitoring. In the example
we are uploading 3 files of 20GB each to a secure webdav server.

```const { createClient } = require("webdav");
const StreamCounter = require('stream-counter');
const fs = require('fs');
 
const client = createClient(
    "https://webdav.server.org",
    {
        username: "user",
        password: "pass"
    }
);


function read_write_file(item) {
 return new Promise( (resolve,reject) => {
 //loading spinner start (e.x. react.JS)
 let counter = new StreamCounter();
 counter.on('progress', () => {
	if ((counter.bytes % 1000000 > 0) && (counter.bytes % 1000000 < 32768)) //tricky ;)
 	 console.log("progress", counter.bytes);
 });
 const r = fs.createReadStream(item);
 const w = client.createWriteStream(item);
 w.on('finish', () => {
  console.log('Loaded.');
  resolve("done");//loading spinner stop (e.x.in react.js)
 });
 r.pipe(w).pipe(counter);
 console.log('wait...to load the '+item);
 })
};

//upload
read_write_file("20G.bin").then( status => {
 console.log(status);
});

//upload
read_write_file("20G_2.bin").then( status => {
 console.log(status);
});

//upload
read_write_file("20G_3.bin").then( status => {
 console.log(status);
});
```
