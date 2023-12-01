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

These are all important decisions for a developer to make, so they should be able to declare these different aspects somewhat separately.
However, not every combination makes sense, some aspects are implied by other aspects, and there are common patterns that can be used by developers who don't want to define all of
these aspects.

As a result, the model shouldn't force developers to answer all of these questions unless they need to.

## Pattern 1: Named template

One option is to give a name to each pattern or "template". The template would provide fixed values for some of these aspects and allow the developer to declare other aspects.

Here are some examples of what this might look like:

**A Java library**: a library implemented using a single version of Java.

The developer declares the Java version, and this also implies the JVM version that the library will run on.

```kotlin
javaLibrary {
    javaVersion = 21
    // other settings
}
```

**A Kotlin JVM library**: a library implemented using a single version of Kotlin and that runs on a single version of the JVM.

The developer declares the Kotlin and JVM versions.

```kotlin
kotlinJvmLibrary {
    kotlinVersion = "1.9.21"
    jvmVersion = 21
    // other settings
}
```

**A Kotlin mobile library**: a library implemented using a single version of Kotlin and that runs on Android and iOS devices.

The developer declares the target version of the devices.

```kotlin
kotlinMobileLibrary {
    kotlinVersion = "1.9.21"
    androidVersion = 21
    iOSVersion = "14.0"
    // other settings
}
```

**A Kotlin multiplatform library**: a library implemented using a single version of Kotlin and that runs on one or more targets.

The developer declares the Kotlin version and the targets and their versions.

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

**A Gradle worker library**: a library that runs in the Gradle worker processes and implemented using Java 8.

The developer does not declare anything further about the software type.

```kotlin
workerLibrary {
    // other settings
}
```

**A JVM library**: a generic library that runs on the JVM.

The developer declares the implementation languages and target JVM versions.

For example, the following library is implemented in Kotlin 1.9.20 and Java 17 and runs on the Java 17 and 21 JVMs: 

```kotlin
jvmLibrary {
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
