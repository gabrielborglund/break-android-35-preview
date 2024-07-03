# break-android-35-preview

This app will compile when using compileSdk 35 and it will run fine on an android 15 device, however it will crash at runtime if run on an android-14 device.

Note that I'm not using any android 15 APIs explicitly, but the way `list.removeFirst()` in the MainActivity is compiled is different when using compileSdk 34 and compileSdk 35.

When using compileSdk 34 we use the kotlin extension function [removeFirst](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove-first.html). The bytecode looks like this: 

```
        18: invokestatic  #27                 // Method kotlin/collections/CollectionsKt.arrayListOf:([Ljava/lang/Object;)Ljava/util/ArrayList;
        21: astore_2
        22: aload_2
        23: checkcast     #29                 // class java/util/List
        26: invokestatic  #33                 // Method kotlin/collections/CollectionsKt.removeFirst:(Ljava/util/List;)Ljava/lang/Object;
```

When using compileSdk 35 which introduces the [JDK 21 List interface](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html#removeFirst()), the bytecode looks like this:
```
        18: invokestatic  #27                 // Method kotlin/collections/CollectionsKt.arrayListOf:([Ljava/lang/Object;)Ljava/util/ArrayList;
        21: astore_2
        22: aload_2
        23: invokevirtual #33                 // Method java/util/ArrayList.removeFirst:()Ljava/lang/Object;
```

The `java.util.ArrayList.removeFirst()` method exists in the runtime library on android 15 devices which means the code works fine, but when running the app on an older device that method doesn't exist and the app crashes.

# Repro steps

build the app `./gradlew app:assemble` and install it using adb `adb install app/build/outputs/apk/debug/app-debug.apk` on an android 14 device. It will crash, but it's not using any android 15 APIs.
