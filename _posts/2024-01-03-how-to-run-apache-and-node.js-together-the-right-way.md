---
title: how to run apache and node.js together the right way
---

### Step 1

Get a VPS that offers **2** or more IP addresses.

### Step 2

From the WHM cPanel, find the menu item `Service Configuration`, select `Apache Configuration` and then click on `Reserved IPs Editor`.

### Step 3

Tick the IP address you **DON'T WANT** Apache to listen to, and write it down so you can use it in the next step. Click `Save`.

### Step 4

Install Node.js, and create a server like this:

    var http = require('http');

    var server = http.createServer(function(req, res) {
      res.writeHead(200);
      res.end('Hello, world!');
    });

    server.listen(80, '111.111.111.111');

Replacing `111.111.111.111` with the IP address you previously reserved from the WHM cPanel.

### Step 5

Stop wasting your time and never listen to those telling you to use `mod_rewrite` to proxy Node.js again.
