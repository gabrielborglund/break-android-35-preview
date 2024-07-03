# break-android-35-preview

This app will compile when using compileSdk 35 and it will run fine on an android 15 device, however it will crash at runtime if run on an android-14 device.

# Repro steps

build the app `./gradlew app:assemble` and install it using adb `adb install app/build/outputs/apk/debug/app-debug.apk` on an android 14 device. It will crash, but it's not using any android 15 APIs.
