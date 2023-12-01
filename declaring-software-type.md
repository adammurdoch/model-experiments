# Declaring the software type

There are several different aspects that make up the "type" of software produced by a project:

- How is the software intended to be used?
    - Is it a CLI application? A web app? A network service? A library? A Gradle plugin?
- Where is the software intended to be run?
    - Which "runtimes" will it run in? Certain OS and architectures? the JVM? Mobile devices? node.js? Gradle?
    - Which version or versions of these things will it run in?
- How is the software implemented?
    - Which source languages does the implementation use?
    - Which version or versions of these languages does it use?

These are all important decisions for a developer to make, so they should be able to define these different aspects somewhat separately.
However, not every combination makes sense, some aspects are implied by other aspects, and there are common patterns that can be used by developers who don't want to define all of
these aspects. As a result, we shouldn't force developers to answer each of these questions separately unless they need to.

<a id="templates"></a>
## Pattern 1: Named template

One option is to give a name to a pattern or template, where the template provides fixed values for these aspects and allows the developer to declare other aspects.

Here are some examples of what this might look like:

**A Java library**: a library implemented using a single version of Java.

The developer defines the Java version, and this also implies the JVM version that the library will run on.

```kotlin
javaLibrary {
    javaVersion = 21
    // other settings
}
```

**A Kotlin JVM library**: a library implemented using a single version of Kotlin and that runs on a single version of the JVM.

The developer defines these versions.

```kotlin
kotlinJvmLibrary {
    kotlinVersion = "1.9.21"
    jvmVersion = 21
    // other settings
}
```

**A Kotlin mobile library**: a library implemented using a single version of Kotlin and that runs on Android and iOS devices.

The developer defines the versions of those devices.

```kotlin
kotlinMobileLibrary {
    kotlinVersion = "1.9.21"
    androidVersion = 21
    iOSVersion = "12.0"
    // other settings
}
```

**A Kotlin multiplatform library**: a library implemented using a single version of Kotlin and that runs on one or more targets.

The developer defines the Kotlin version and the targets.

```kotlin
kotlinLibrary {
    kotlinVersion = "1.9.21"
    targets {
        jvm(17)
        browser("ECMAScript 2019")
        android(24)
        macosArm64("14.0")
    }
    // other settings
}
```

**A Gradle worker library**: a library that runs in the Gradle worker processes.

It is implemented using Java 8 and the developer does not define anything further.

```kotlin
workerLibrary {
    // other settings
}
```

**A JVM library**: a generic library that runs on the JVM.

The developer defines the implementation languages and target JVM versions.

```kotlin
library {
    languages {
        kotlin("1.9.20")
        java(17)
    }
    targets {
        jvm(17)
        jvm(21)
    }
    // other settings
}
```
