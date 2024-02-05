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

The examples below use several different patterns to define a given software type:

- Developer declares each aspect explicitly
    - pattern 1a: Using a block to represent the software usage with the other details nested. Plugin are not explicitly applied.
- Developer uses a named template and fills in the missing details, if any
    - pattern 2a: Using a block to represent the named template. Plugin are not explicitly applied.

The template in pattern 2 would provide fixed values for some of the software aspects and allow the developer to declare other aspects. Some or even all of these declarable aspects
can have default values.

In the examples below, you can see that pattern 1 is very flexible and can express a wide range of software. However, not every combination of implementation language (version) and
runtime (version) makes sense or is supported. Pattern 2, on the other hand, allows the contents of the block to be strongly typed based on the name of the template.

For beginners, the "implementation" and "runtime" concepts are unnecessary, particularly for Java applications. Pattern 1 exposes these to every developer, whereas pattern 2 allows
a simplified view.

Patterns 1 and 2 are not mutually exclusive. Pattern 2 is just a more constrained way to express pattern 1

## JVM CLI application implemented using Java

**Pattern 1a**

```kotlin

// Note: no explicit plugin application. This is inferred from the declaration

// A general purpose CLI application type
cliApplication {

    // Implementation languages
    implementation {
        java(21) {
            // Some Java 21 language settings
        }
    }

    // Target runtimes for the application
    runtime {
        jvm(21) {
            // Some Java 21 runtime settings
        }
    }
}
```

This could be simplified by using a convention for the target runtimes for software components with a Java implementation:

```kotlin
cliApplication {
    implementation {
        java(21)
    }

    // Use Java 21 as the runtime as well
}
```

**Pattern 2a**

Use a template for a CLI application implemented using a single version of Java.

The developer declares the Java version and this implies the Java language version and target JVM version.

```kotlin
javaCliApplication {
    javaVersion = 21
    // other settings
}
```

## JVM CLI application implemented using Kotlin

**Pattern 1a**

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22")
    }
    runtime {
        jvm(21)
    }
}
```

**Pattern 2a**

Use a template for a CLI application implemented using a single version of Kotlin and that targets a single version of the JVM.

The developer declares the Kotlin and JVM versions.

```kotlin
kotlinCliApplication {
    kotlinVersion = "1.9.22"
    jvmVersion = 21
    // other settings
}
```

## JVM CLI application implemented using Java and Kotlin

**Pattern 1a**

```kotlin
cliApplication {
    implementation {
        java(21)
        kotlin("1.9.22")
    }
    runtime {
        jvm(21)
    }
}
```

This can be simplified with a convention for the target JVM version:

```kotlin
cliApplication {
    implementation {
        java(21)
        kotlin("1.9.22")
    }
    // Use Java 21 as the runtime as well
}
```

**Pattern 2a**

Use a template for a CLI application that is implemented using Java and Kotlin

```kotlin
kotlinAndJavaCliApplication {
    javaVersion = 21
    kotlinVersion = "1.9.22"
}
```

## JVM CLI application implemented using Java and Kotlin with JVM-specific code

**Pattern 1a**

```kotlin
cliApplication {
    implementation {
        java(17)
        java(21)
        kotlin("1.9.22")
    }
    runtime {
        jvm(17)
        jvm(21)
    }
}
```

**Pattern 2a**

This will look roughly the same as the above.

## JVM CLI application implemented using Java with tests implemented in Groovy

**Pattern 1a**

```kotlin
cliApplication {
    implementation {
        java(21)
    }
    runtime {
        jvm(21)
    }
    tests {
        unit {
            implementation {
                groovy("5.0")
            }
        }
    }
}
```

**Pattern 2a**

Use a template for a Java CLI application with Groovy tests:

```kotlin
javaCliApplicationWithGroovyTests {
    javaVersion = 21
    tests {
        groovyVersion = "5.0"
    }
}
```

## Native CLI application implemented using Kotlin and Swift

**Pattern 1a**

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22")
        swift("5.9")
    }
    runtime {
        macOS("12.0", aarch64)
        macOS("12.0", x64)
        linux(glibc, x64)
    }
}
```

**Pattern 2a**

Use a template for a generic native CLI application

## CLI application implemented using Kotlin and packaged as a JVM application and as native executables

**Pattern 1a**

For example, when some specific native code is used for macOS but the JVM is used for the other platforms.

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22")
    }
    runtime {
        jvm(21)
        macOS("12.0", aarch64)
        macOS("12.0", x64)
    }
}
```

## Android application implemented using Java

**Pattern 1a**

```kotlin
mobileApplication {
    implementation {
        java(21)
    }
    runtime {
        android("12.0")
    }
}
```

## Android application implemented using Kotlin and Java

**Pattern 1a**

```kotlin
mobileApplication {
    implementation {
        java(21)
        kotlin("1.9.22")
    }
    runtime {
        android("12.0")
    }
}
```

## Android application implemented using Kotlin, Java and C++

**Pattern 1a**

```kotlin
mobileApplication {
    implementation {
        java(21)
        kotlin("1.9.22")
        cpp("c++20")
    }
    runtime {
        android("12.0")
    }
}
```

## iOS application implemented using Kotlin and Swift

**Pattern 1a**

```kotlin
mobileApplication {
    implementation {
        kotlin("1.9.22")
        swift("5.9")
    }
    runtime {
        iOS("14.0")
    }
}
```

## Mobile application implemented using Kotlin that runs on iOS and Android

**Pattern 1a**

```kotlin
mobileApplication {
    implementation {
        kotlin("1.9.22")
    }
    runtime {
        android("12.0")
        iOS("14.0")
    }
}
```

## Library implemented using Kotlin that runs on the JVM

**Pattern 1a**

```kotlin
library {
    implementation {
        kotlin("1.9.22")
    }
    runtime {
        jvm(21)
    }
}
```

## Library implemented using Kotlin runs on the JVM and other targets

**Pattern 1a**

```kotlin
library {
    implementation {
        kotlin("1.9.22")
    }
    runtime {
        jvm(21)
        browser("ECMAScript 2019")
        macOS("12.0", aarch64)
        macOS("12.0", x64)
        linux(glibc, x64)
    }
}
```

## Gradle worker library

A library that runs in the Gradle worker processes and implemented using Java 8.

**Pattern 2a**

```kotlin
workerLibrary {
    // other settings
}
```

## Pattern 2: Named template

Here are some examples of what this might look like:

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
        jvm(21)
        browser("ECMAScript 2019")
        android("12.0")
        macOS("12.0", aarch64)
        macOS("12.0", x64)
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
        kotlin("1.9.22")
        java(21)
    }
}
```
