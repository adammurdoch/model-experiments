# Declaring the software type

The "type" of software produced by a project is made up of several aspects:

- How is the software intended to be used?
    - Is it a CLI application? A web app? A network service? A library? A Gradle plugin?
- Where is the software intended to be run?
    - Which "runtimes" will it run in? Certain OS and architectures? the JVM? Mobile devices? node.js? Gradle?
    - Which version or versions of these things will it run in?
- How is the software implemented?
    - Which source languages does the implementation use?
    - Which version or versions of these languages does it use?

These are all important decisions for a developer to make, so they should be able to declare these different aspects somewhat separately.

## Pattern 1: Declare everything explicitly

A pattern we could use is to require that the developer explicitly define each of these aspects - for example "this is a CLI application that runs on the Java 21 JVM and is
implemented using Java 21".

**JVM CLI application implemented using Java**

```kotlin
// A general purpose CLI application
cliApplication {
    implementation {
        java(21) {
            // Some Java 21 language settings
        }
    }
    runtime {
        jvm(21) {
            // Some Java 21 runtime settings
        }
    }
}
```

**JVM CLI application implemented using Kotlin**

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22") { }
    }
    runtime {
        jvm(21) { }
    }
}
```

**JVM CLI application implemented using Java and Kotlin**

```kotlin
cliApplication {
    implementation {
        java(21) { }
        kotlin("1.9.22") { }
    }
    runtime {
        jvm(21) { }
    }
}
```

**Native CLI application implemented using Kotlin and Swift**

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22") { }
        swift("5.9") { }
    }
    runtime {
        macOS("12.0", aarch64) { }
        macOS("12.0", x64) { }
        linux(glibc, x64) { }
    }
}
```

**CLI application implemented using Kotlin and packaged as a JVM application and as native executables**

For example, when some specific native code is used for macOS but the JVM is used for the other platforms.

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22") { }
    }
    runtime {
        jvm(21) { }
        macOS("12.0", aarch64) { }
        macOS("12.0", x64) { }
    }
}
```

**Android application implemented using Java**

```kotlin
mobileApplication {
    implementation {
        java(21) { }
    }
    runtime {
        android(11) { }
    }
}
```

**Android application implemented using Kotlin and Java**

```kotlin
mobileApplication {
    implementation {
        java(21) { }
        kotlin("1.9.22") { }
    }
    runtime {
        android(11) { }
    }
}
```

**iOS application implemented using Kotlin and Swift**

```kotlin
mobileApplication {
    implementation {
        kotlin("1.9.22") { }
        swift("5.9") { }
    }
    runtime {
        iOS(15) { }
    }
}
```

**Mobile application implemented using Kotlin that runs on iOS and Android**

```kotlin
mobileApplication {
    implementation {
        kotlin("1.9.22") { }
    }
    runtime {
        android(11) { }
        iOS(15) { }
    }
}
```

**Kotlin library that runs on the JVM**

```kotlin
library {
    implementation {
        kotlin("1.9.22") { }
    }
    runtime {
        jvm(21) { }
    }
}
```

**Kotlin library that runs on the JVM and other targets **

```kotlin
library {
    implementation {
        kotlin("1.9.22") { }
    }
    runtime {
        jvm(21) { }
        browser("ECMAScript 2019")
        nodeJs() { }
        macOS("12.0", aarch64) { }
        macOS("12.0", x64) { }
        linux(glibc, x64) { }
    }
}
```

This pattern is very flexible. However, not every combination of implementation languages and runtimes makes sense or is supported. It would be good to use a pattern that allows
the IDE, via a schema, to guide the developer to the valid combinations.

For beginners, the distinction between "implementation" and "runtime" is unnecessary, particularly for Java applications.

## Pattern 2: Named template

Another option is to give a name to each pattern or "template". The template would provide fixed values for some of these aspects and allow the developer to declare other aspects.
Some or even all of these declarable aspects can have default values.

This pattern is not mutually exclusive to the previous pattern. The following are just ways of expressing the previous in a more constrained way. 

Here are some examples of what this might look like:

**A Java library**: a library implemented using a single version of Java.

The developer declares the Java version, and this also implies the JVM version that the library targets.

```kotlin
javaLibrary {
    javaVersion = 21
    // other settings
}
```

**A Kotlin JVM library**: a library implemented using a single version of Kotlin and that targets a single version of the JVM.

The developer declares the Kotlin and JVM versions.

```kotlin
kotlinJvmLibrary {
    kotlinVersion = "1.9.22"
    jvmVersion = 21
    // other settings
}
```

**A Kotlin mobile library**: a library implemented using a single version of Kotlin and that runs on Android and iOS devices.

The developer declares the target version of the devices.

```kotlin
kotlinMobileLibrary {
    kotlinVersion = "1.9.22"
    androidVersion = "12.0"
    iOSVersion = "14.0"
    // other settings
}
```

**A Kotlin multiplatform library**: a library implemented using a single version of Kotlin and that runs on one or more targets.

The developer declares the Kotlin language version and the targets and their versions.

```kotlin
kotlinLibrary {
    kotlinVersion = "1.9.22"
    targets {
        jvm(17)
        browser("ECMAScript 2019")
        android("12.0")
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

For example, the following library is implemented in Kotlin 1.9.20 and Java 17 and targets the Java 17 and 21 JVMs:

```kotlin
jvmLibrary {
    implementation {
        kotlin("1.9.20")
        java(17)
    }
    runtime {
        jvm(17)
        jvm(21)
    }
    // other settings
}
```

**An Android application**: an application that runs on Android devices.

The developer declares the implementation languages and target Android version.

```kotlin
androidApplication {
    androidVersion = "12.0"
    implementation {
        kotlin("1.9.20")
        java(17)
    }
}
```
