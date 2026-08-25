# sonic-2-modloader

"WARNING: THIS NEEDS YOU TO HAVE LINUX OR MAC OS IF IT WORKS ON THERE WINDOWS IS NOT SUPPORTED WSL CAN WORK BUT IT IS NOT RECOMMEND why you may ask to build the game! not to run it. "

HELLO!

This is a **WIP Sonic 2 modloader project**.

You need to provide your **own legally obtained Sonic 2 APK**. The Sonic 2 APK is **not included in this repository**.

We are currently working with **Sonic 2 version 2.40**.

The main goal is pretty simple:

**make our own modloader and figure out how to get it talking to the actual game.**

We tried using `setGlobalVariable` before, but we couldn't figure out how it actually worked, so instead of spending forever guessing, we're making our own system.

# What works right now

A surprising amount, actually.

* The modloader starts
* `ModAPI` initializes
* `ModAPI.tick()` runs continuously
* Our code can talk to Android
* Our code can create files
* Our code can draw things on the screen
* Sonic 2 keeps running normally
* ARM64 works on Waydroid
* Sonic 2 version 2.40 works with our current setup
* We can see the modloader running through logcat

For example:

```text
Sonic2Mod: ModAPI initialized
Sonic2Mod: Created hello_world.txt
```

And our tick function keeps running:

```text
Sonic2Mod: ModAPI tick
Sonic2Mod: ModAPI tick
Sonic2Mod: ModAPI tick
```

So yeah, the modloader is definitely alive.

# How it works right now

The current setup is basically:

```text
Sonic 2
   ↓
Sonic2Render / RetroEngine
   ↓
our modloader
   ↓
ModAPI
   ↓
our code
```

The Android side is working.

The annoying part is that our code still needs to actually **talk to the game's engine**.

That's the part we're working on now.

# ModAPI

The ModAPI is going to be the thing mods use instead of having to mess with the game's internals themselves.

Right now it's extremely basic.

We have:

```text
ModAPI.tick()
```

which gets called while the game is running.

We're using this for testing right now, mainly to make sure the API isn't randomly dying somewhere.

Eventually this is where things like player information, game state, objects, and other game stuff will live.

The API is still being designed, so don't expect the names or functions to stay exactly the same.

# Talking to the actual game

This is the big one.

We found the game's native library:

```text
lib/arm64-v8a/libRetroEngineJNI.so
```

and started digging through it.

While looking around, we found some interesting strings:

```text
player.lives
player.score
player.scoreBonus
```

For example:

```text
446fd5 player.lives
446fe2 player.score
446fef player.scoreBonus
```

We also found things like:

```text
ProcessPlayerControl
ProcessPlayerTileCollisions
SetPlayerScreenPosition
SetPlayerLockedScreenPosition
```

This is useful because it gives us places to start looking.

However, finding a string called `player.lives` **doesn't mean we've found the lives variable itself**.

It's just a string sitting in the binary.

The next step is finding the native code that uses it and figuring out how the engine actually stores and accesses that data.

Once we understand that, our API can actually communicate with the game instead of just talking to Android.

# Lua

Lua isn't implemented yet.

That's coming later.

The idea is that Lua mods will use our ModAPI instead of directly poking around inside the native library.

Something like this eventually:

```lua
local lives = Player.getLives()

Player.setLives(99)
```

But that's future stuff.

Right now we're still working on the much more important problem of:

**HOW THE HELL DO WE TALK TO THE GAME?**

# Current test

For now, the modloader creates:

```text
hello_world.txt
```

when it starts.

This isn't meant to be some amazing feature.

It's just a really easy way to prove:

> yep, our code actually ran.

We also have the log output from `ModAPI.tick()` to prove that the API keeps running.

# What's still missing

There's still a lot to do.

* Game ↔ ModAPI communication
* Reading game variables
* Changing game variables
* Proper ModAPI
* Lua support
* Lua ↔ ModAPI communication
* Proper mod loading
* More architectures
* More APK configurations

Other architectures/configurations **might work for you**.

If you somehow get a split APK or another setup working, good for you because I haven't tested every possible configuration.

# Requirements

For the current setup you'll need:

* APKTool
* ADB
* `zipalign`
* `apksigner`
* An ARM64 Android/Waydroid environment
* Sonic 2 version 2.40
* Root access for the current setup

# Building

Build the APK:

```bash
apktool b sonic2-apktool \
    -o sonic2-mod-test-unsigned.apk
```

Align it:

```bash
zipalign -f -p 4 \
    sonic2-mod-test-unsigned.apk \
    sonic2-mod-test-aligned.apk
```

Sign it:

```bash
apksigner sign \
    --ks "YOUR_SIGN_KEY" \
    --out sonic2-mod-test.apk \
    sonic2-mod-test-aligned.apk
```

**DO NOT upload your keystore or keystore password to GitHub.**

Seriously.

# Installing on Waydroid

Install it with:

```bash
adb install sonic2-mod-test.apk
```

If incremental installation causes problems:

```bash
adb install --no-incremental sonic2-mod-test.apk
```

For split APKs, you'll need to use:

```bash
adb install-multiple --no-incremental
```

with the APKs your installation actually needs.

# Important

This repository does **not** contain the Sonic 2 APK or other proprietary game files.

You need to get the game yourself and work with your own copy.

The repository is for the **modloader code, ModAPI, Lua system, and other development code**.

We're still figuring a lot of this out, so things will probably break.

That's kinda the point.

If you find a bug or want to help with the project, (or want a premade apk), email me:

[githubemail.possibly978@passinbox.com](mailto:githubemail.possibly978@passinbox.com)

# Status

**VERY MUCH A WORK IN PROGRESS**

The modloader can run.

The API can run.

Android communication works.

Now we just need to make the damn thing talk to Sonic 2.
