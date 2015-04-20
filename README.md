# KSUUID

## Description
This plugin allows you to generate a reasonably-persistent UUID for iOS by utilizing the keystore.

## Usage
Add the plugin:

```sh
cordova plugin add https://github.com/embarkmobile/cordova-ios-uuid
```

Use:
```js
window.KSUUID.get(
	function(uuid) {
		console.log("UUID: " + uuid);
	},
	function(err) {
		console.log("Error retrieving UUID: ", err);
	}
);
```

The UUID is persistent across application installs.  To reset it, use the `KSUUID.reset()` function.

## License
Copyright (c) 2015 Embark Mobile. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0.
