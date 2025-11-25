# APK modding

This repo is all about modding apks. We require apktool and androidsdk
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

# Step 1.5: Get the APK

If an app is already installed within waydroid, we can grab the apk directly from the device:
```
sudo cp ~/.local/share/waydroid/data/app/[app_hash]/[package_name]/base.apk
```

In the case that an app is 'split', we have some extra steps to cleanly recompile.

```
sudo cp ~/.local/share/waydroid/data/app/[app_hash]/[package_name]/*.apk .
apktool d -f [do all split apks one by one]
cp -rn [do all split apks one by one]/* [merged_dir]/.
```

Then, in order to have the linker locate everything, we must add all the apktool.yml 'doNotCompress' sections to the base apktool.yml.
We also must make sure we remove both android:requiredSplitTypes and android:splitTypes from the AndroidManifest.xml.

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
We can reuse this generated key for every single apk if we want.
```
keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 1000000 -storepass Mypass
```
For older apks (V1) we can use jarsigner:
```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore my_application.apk alias_name -storepass Mypass
```
For newer apks (V2+) we should just use apksigner:
```
[path_to_androidsdk]/apksigner sign -ks [keystore] [apk]
```

# Notes

For Fedora, in order to get apktool, look here https://apktool.org/docs/install/. Basically, we're getting the scripts and placing in our bin.
In order to get apksigner and zipalign, go through software/flatpak and install android-studio. During setup, we can select to install only the sdk.
