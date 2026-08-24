# sonic-2-modloader

HELLO!

This is a **WIP Sonic 2 modloader project**.

You will need to provide **your own legally obtained Sonic 2 APK**. The Sonic 2 APK is **not included** in this repository.

The current version works by injecting the mod initialization code into `Sonic2Activity.smali`.

## What works

* The injected code runs when the game starts
* The mod code can communicate with Android
* The mod code can create files
* The game continues running normally
* Code can draw things on the screen
* ARM64 currently works on Waydroid

## What doesn't work yet

Nothing currently known to be broken with the basic test.

If you find a bug, please open an issue on GitHub.

## What's not done

* Modloader is still a work in progress
* ModAPI is still a work in progress
* Lua support isn't implemented yet
* Other architectures/configurations aren't supported yet for me it may work for you if you dont be a idiot and somehow find a split apk

# Injecting the Modloader

## 1. Decompile your Sonic 2 APK

Use APKTool to decompile your own APK.

Find:

```text
Sonic2Activity.smali
```

It should be located somewhere similar to:

```text
smali_classes2/com/sega/sonic2px/Sonic2Activity.smali
```

## 2. Find the injection point

Open `Sonic2Activity.smali` and search for:

```smali
invoke-virtual {v0, v5}, Lcom/sega/sonic2px/Sonic2View;->setKeepScreenOn(Z)V
```

Immediately underneath that line, insert:

```smali
# ===== MOD TEST =====

new-instance v0, Lcom/sega/sonic2px/mod/ModTestView;

invoke-direct {v0, p0}, Lcom/sega/sonic2px/mod/ModTestView;-><init>(Landroid/content/Context;)V

new-instance v1, Landroid/view/ViewGroup$LayoutParams;

const/4 v2, -0x1

const/4 v3, -0x1

invoke-direct {v1, v2, v3}, Landroid/view/ViewGroup$LayoutParams;-><init>(II)V

invoke-virtual {p0, v0, v1}, Landroid/app/Activity;->addContentView(Landroid/view/View;Landroid/view/ViewGroup$LayoutParams;)V

# ===== END MOD TEST =====
```

## 3. Add the mod code

Copy the project's `mod` package into:

```text
smali_classes2/com/sega/sonic2px/
```

You should end up with something similar to:

```text
smali_classes2/
└── com/
    └── sega/
        └── sonic2px/
            ├── Sonic2Activity.smali
            ├── Sonic2View.smali
            └── mod/
                ├── ModAPI.smali
                └── ModTestView.smali
```

The injected code expects:

```text
com.sega.sonic2px.mod.ModTestView
```

so the package/folder structure needs to remain correct.

# Building

## Requirements

You will need:

* APKTool
* ADB
* `zipalign`
* `apksigner`

### Build the APK

```bash
apktool b sonic2-apktool \
    -o sonic2-mod-test-unsigned.apk

zipalign -f -p 4 \
    sonic2-mod-test-unsigned.apk \
    sonic2-mod-test-aligned.apk

apksigner sign \
    --ks "YOUR_SIGN_KEY" \
    --out sonic2-mod-test.apk \
    sonic2-mod-test-aligned.apk
```

**Do not upload your keystore or keystore password to GitHub.**

## Installing on Waydroid

install the modified APK:

```bash
adb install sonic2-mod-test.apk
```

If your Waydroid installation does not allow incremental installation, use:

```bash
adb install --no-incremental sonic2-mod-test.apk
```

For split APK installations, use `adb install-multiple --no-incremental` with the APKs required by your configuration.

## Important

This project does **not** distribute the Sonic 2 APK.

You must obtain the game yourself and apply the modifications to your own copy.

This project is a WIP and is provided for experimentation and modding development.

If you find bugs or want to help improve the code, email me: [githubemail.possibly978@passinbox.com](mailto:githubemail.possibly978@passinbox.com)


## things

The app needs root and for now it makes a file called hello world so it talks with android just fine

The app (patch) still doesnt talk with the game, Will be fixed in a later ver
