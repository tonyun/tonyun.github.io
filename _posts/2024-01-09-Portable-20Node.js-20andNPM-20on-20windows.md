---
title: Portable Node.js and NPM on windows
---

1. Get node binary (node.exe) from http://nodejs.org/download/
2. Create the folder where node will reside and move node.exe to it
3. Download the last zip version of npm from http://nodejs.org/dist/npm
4. Unpack the zip inside the node folder
5. Download the last tgz version of npm from http://nodejs.org/dist/npm
6. Open the tgz file and unpack only the file **bin/npm** (without extension) directly on the node folder.
7. Add the the node folder and the packages/bin folder to PATH
8. On a command prompt execute ```npm install -g npm``` to update npm to the latest version

Now you can use npm and node from windows cmd or from bash shell like Git Bash of msysgit.