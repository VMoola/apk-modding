# APK modding

This repo is all about modding apks. We require apktool and jarsigner
(from jdk). We also use waydroid for testing.

# Instructions

# Step 1: Setup Waydroid for Testing
With nvidia graphics: in /var/lib/waydroid/waydroid.cfg
[properties]
ro.hardware.gralloc=default
ro.hardware.egl=swiftshader

Because our waydroid is x86_64, we need to have an emulation layer, simpy follow https://github.com/casualsnek/waydroid_script.git and install libhoudini for arm64
Use “waydroid app install [path]” to install the apk. Uninstall the regular way (drag and drop).
Also useful: Within waydroid: (Settings -> about phone, spam click build -> Developer options)

Now we have our testing basis. We can send these apks to install regularly if they run in waydroid.

# Step 2: Decompile the APK
Apktool decompiles: apktool d -f [file].apk
If apktool hasn't been setup yet, look at https://apktool.org/docs/install/.

# Step 3: Mod the Decompiled APK
Make a backup, because our edits may not actually compile.
Depending on the game, we may need different decoders
*Flash games (.swf) need JPECS. (Can be found in Fedora through flathub)

# Step 4: Recompile the APK
Apktool recompiles: apktool b -f –use-aapt -d [apkname]

# Step 5: Sign the APK
```
keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 1000000 -storepass Mypass
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore my_application.apk alias_name -storepass Mypass
```
We can reuse the generated key for every other apk if we want.
