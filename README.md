# sonic-2-modloader
this is a wip project this  can show the app can talk to android but not the game yet "sadly made using ai but someone can help fix this code if they want"
this works by injecting a smali file into sonic2activity which allows for a modloader

WIP! 
what works:
it can make files but you need root to see them
the game runs fine
code loading works fine and you can draw thing on the screen

what doesnt work:
nothing
if you find bugs email me using "githubemail.possibly978@passinbox.com"


whats not done:
modloader still a work in progress
modapi is still not done
lua still isnt here
for more stuff other then arm64


how to build:
1.clone the project
2. you need to have apktool adb zipalign and apksigner

3. build the apk
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

4. download the config.arm64_v8a-signed.apk 

this only works with a dpi of 180 use with waydroid


download config.ldpi-signed.apk

after you are done


use this adb cmd

```bash
adb install-multiple --no-incremental \
    sonic2-mod-test.apk \
    config.arm64_v8a-signed.apk \
    config.ldpi-signed.apk
```

or you can download the zip file which has all the apps made for you :)


 5. download apktool 3.03 here https://github.com/iBotPeaches/Apktool/releases
