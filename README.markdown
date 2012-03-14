# Unique Identifier Is Dead, Long Live Unique Identifier

As of iOS 5, Apple has deprecated the device unique identifier api and hasn’t provided a friendly Obj-C replacement, instead recommending [`CFUUIDCreate` and `NSUserDefaults`](https://developer.apple.com/library/ios/#documentation/uikit/reference/UIDevice_Class/DeprecationAppendix/AppendixADeprecatedAPI.html).

`CFUUIDCreate` isn’t very complicated and neither is `NSUserDefaults`, but this solution fails in a few different ways:

+ It’s not a quick one-shot call to get the UUID; you have to write your own wrapper to make it friendly
+ It doesn’t persist; deleting the app blows away the UUID
+ There’s no way to share it between apps

The answer to these issues is to create a class that acts as the wrapper and improves on the persistence and sharing options using Keychain.

## Quick Keychain primer:

> On iOS each apps gets its own keychain where it can store sensitive data that only that app can access, with some very particular exceptions. This keychain is persisted across installations and, if the user encrypts their backups, across restores. A full restore and setup as a new device or restore from unencrypted backup wipes the keychain.

This gives us a place to store an app (or app suite) specific UUID that will last as long as a given user continues using that device, but that will change if they do a full restore. That’s a nice level of fidelity that suits most legitimate tracking needs, like identifying that a device is already known to a web service tracking active devices. A given device won’t have two UUIDs simultaneously and for most users won’t change until they get a new device.

Another useful feature of Keychain is support for access groups, which allow iOS applications from the same provider to opt in to sharing a common keychain. The UUID can be stored in a keychain that all the apps can access, thus allowing us to know that a given set of installations all represent a single device.

> “So this all seems interesting, but where’s the code?” – Nerds Still Reading This

     @interface BPXLUUIDHandler : NSObject
     /*
      * Retrieve UUID from keychain, if one does not exist, generate one and store it in the keychain.
      * UUIDs stored in the keychain will perisist across application installs
      * but not across device restores.
      */
     + (NSString *)UUID;
     /*
      * Remove stored UUID from keychain
      /
     + (void)reset;
     /*
      * Getter/setter for access group used for reading/writing from keychain.
      * Useful for shared keychain access across applications with the
      * same bundle seed (requires properly configured provisioning and entitlements)
      */
     + (NSString *)accessGroup;
     + (void)setAccessGroup:(NSString *)accessGroup;
     @end

`BPXLUUIDHandler` is a class we created to encapsulate all the handling of getting/storing the new UUID and added support for access groups since some of our clients have application suites.

It supports ARC and non ARC configurations and tucks away all the keychain interaction so that we don’t have to keep writing the same blob of keychain queries again and again.

Use and enjoy and provide feedback.

—[Unique Identifier Is Dead, Long Live Unique Identifier](http://blackpixel.com/blog/1639/unique-identifier-is-dead-long-live-unique-identifier/)

# How to

+ Import BPXLUUIDHandler.h
+ Retrieve the UUID with `[BPXLUUIDHandler UUID]`
+ Done!

Non-ARC projects are support out of the box.

# License

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0.
