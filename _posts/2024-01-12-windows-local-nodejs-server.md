---
title: How to Setup Windows as a Node.js Server Environment
---

Install the following in this order:

1. [cmder](https://github.com/cmderdev/cmder#installation) | Console Emulator for Windows. Use the `Download Full` link.
	  * Download and unzip into `C:\Users\your-username-here\cmder`.
	  * Open the `C:\Users\your-username-here\cmder` directory right click on `cmdre.exe` click `Pin to Taskbar`.
2. [nvm-windows](https://github.com/coreybutler/nvm-windows#installation--upgrades) | Node Version Manager for Windows.
	  * Download `nvm-setup.zip`.
	  * Extract the `nvm-setup.exe` file somewhere and run it.
	  * TODO: The installer isn't updating the `PATH` correctly so the `node`, `npm` and `nvm` commands may not work. Make sure to add the following to Window's `PATH`:
	    * Open the `cmder` console you installed above and run:
			```
			setx path "%path%;C:\Users\your-username-here\AppData\Roaming\nvm;C:\Program Files\nodejs;"
			```
	  * run:
	  
		  ```
		  nvm install 8.9.4
		  nvm use 8.9.4
		  node -v
		  # Should return 8.9.4
		```
3. [windows-build-tools](https://github.com/felixrieseberg/windows-build-tools) | C++ Build Tools for Windows using npm

		> Note: This will take 10 - 30 minutes to complete.
    * Right click on the `cmder` console icon you installed above and run it as an Administrator. Then run:
	  * `npm install --global --production windows-build-tools`

You should now have the commands, `node`, `npm`, `nvm` and `git` available from any command line or `cmder` console.
