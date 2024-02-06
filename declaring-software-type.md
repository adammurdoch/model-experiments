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

- Pattern 1: developer declares each aspect separately
    - pattern 1a: Using a block to represent the software usage with the other details nested. Plugin are not explicitly applied.
- Pattern 2: developer uses a named template and fills in the missing details, if any
    - pattern 2a: Using a top level block to represent the named template. Plugin are not explicitly applied.
    - pattern 2b: Using nested blocks to represent the named template. Plugin are not explicitly applied.

The template in pattern 2 would provide fixed values for some of the software aspects and allow the developer to declare other aspects. Some or even all of these declarable aspects
can have default values.

Pattern 1 is very flexible and can express a wide range of software. However, not every combination of implementation language (version) and runtime (version) makes sense or is
supported. Pattern 2, on the other hand, allows the contents of the block to be strongly typed based on the template and so constrain the available combinations.

Pattern 1 exposes the "implementation" and "runtime" concepts to every developer. This is often unnecessary, particularly for beginners writing Java software. Pattern 2 allows
a simplified view that captures a specific pattern.

Patterns 1 and 2 are not mutually exclusive. Pattern 2 is just a more constrained way to express pattern 1, and every pattern 2 can be transformed into a pattern 1.

## JVM CLI application implemented using Java

**Pattern 1a**

```kotlin

// Note: no explicit plugin application. This is inferred from the declaration

// A general purpose CLI application type
cliApplication {

    // Declare the implementation languages. At least one implementation language is required
    implementation {
        java(21) {
            // Some Java 21 language settings
        }
    }

    // Declare the target runtimes for the application. At least one runtime is required
    runtime {
        jvm(21) {
            // Some Java 21 runtime settings
        }
    }
}
```

This can be simplified by using a convention for the target runtimes for software components with a Java implementation:

```kotlin
cliApplication {
    implementation {
        java(21)
    }

    // Use Java 21 as the runtime as well, with default settings
}
```

**Pattern 2a**

Use a template for a CLI application implemented using a single version of Java.

The developer declares the Java version and this implies the Java language version and target JVM version.

```kotlin

// Use a named template or "type" 
javaCliApplication {

    // This is required
    javaVersion = 21

    // Java 21 language settings
    // Java 21 runtime settings
}
```

This template expands to something like:

```kotlin
cliApplication {
    implementation {
        java($javaVersion)
    }
    runtime {
        jvm($javaVersion)
    }
}
```

**Pattern 2a**

Use a nested template that defines Java as both the implementation and target for a software component:

```kotlin

// Use the general purpose CLI application type
cliApplication {

    // A template that defines the implementation language and target runtime
    java(21)

    // potentially, cannot use the `implementation { }` and `runtime { }` blocks because these are already implied
}
```

## JVM CLI application implemented using Kotlin

**Pattern 1a**

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22") {
            // Kotlin language settings
        }
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

**Pattern 2b**

```kotlin
cliApplication {
    // A template that defines Kotlin as the single implementation language
    kotlin("1.9.22")

    // A template that defines the JVM as the single target runtime
    jvm(21)
}
```

or perhaps:

```kotlin
cliApplication {
    kotlinJvm("1.9.22", 21)
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

**Pattern 2b**

```kotlin
cliApplication {
    java(21)
    kotlin("1.9.22")
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

It's unlikely we'd use a template called something like "javaAndKotlinCliApplicationWithMultipleTargets { }". It's more likely the template would be something custom called, for
example, "myProductApp":

```kotlin
myProductApp {
    // All of the above is already implied
}
```

**Pattern 2b**

```kotlin
cliApplication {
    java(17)
    java(21)
    kotlin("1.9.22")
}
```

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
            runtime {
                jvm(21)
            }
        }
    }
}
```

Tests might be split out as a separate top-level block:

```kotlin
cliApplication {
    implementation {
        java(21)
    }
    runtime {
        jvm(21)
    }
}

tests {
    unit {
        implementation {
            groovy("5.0")
        }
        runtime {
            jvm(21)
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

**Pattern 2b**

```kotlin
cliApplication {
    java(21)
    tests {
        // A template that defines the implementation language for the test suites
        groovy("5.0")
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

This could be simplified using a convention that uses Kotlin's "tier 1" targets (different to the above):

```kotlin
cliApplication {
    implementation {
        kotlin("1.9.22")
        swift("5.9")
    }
}
```

**Pattern 2a**

Use a template for a native CLI application implemented using Kotlin and Swift:

```kotlin
kotlinAndSwiftCliApplication {
    kotlinVersion = "1.9.22"
    swiftVersion = "5.9"
    runtime {
        macOS("12.0", aarch64)
        macOS("12.0", x64)
        linux(glibc, x64)
    }
}
```

**Pattern 2b**

```kotlin
cliApplication {
    // Templates for implementation languages
    kotlin("1.9.22")
    swift("5.9")
  
    // A template for the target runtimes 
    myNativeTargets()
}
```

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

**Pattern 2a**

```kotlin
kotlinCliApplication {
    kotlinVersion = "1.9.22"
    runtime {
        jvm(21)
        macOS("12.0", aarch64)
        macOS("12.0", x64)
    }
}
```

**Pattern 2b**

```kotlin
cliApplication {
    kotlin("1.9.22")

    macOS("12.0")
    jvm(21)
}
```

## Android application implemented using Java

**Pattern 1a**

```kotlin
// A general purpose "mobile" application
mobileApplication {
    implementation {
        java(21)
    }
    runtime {
        android("12.0")
    }
}
```

**Pattern 2a**

```kotlin
javaAndroidApplication {
    javaVersion = 21
    androidVersion = "12.0"
}
```

**Pattern 2b**

```kotlin
mobileApplication {
    // Templates for implementation language and target runtime
    android("12.0")
    java(21)
}
```

or perhaps

```kotlin
mobileApplication {
    javaAndroid(21, "12.0")
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

**Pattern 2a**

```kotlin
javaAndKotlinAndroidApplication {
    javaVersion = 21
    kotlinVersion = "12.0"
    androidVersion = "12.0"
}
```

**Pattern 2b**

```kotlin
mobileApplication {
    // Templates for implementation language and target runtime
    android("12.0")
    kotlin("1.9.22")
    java(21)
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

**Pattern 2a**

Something like:

```kotlin
myProductApp {
    // Implies all of the above
}
```

**Pattern 2b**

```kotlin
mobileApplication {
    // Templates for implementation language and target runtime
    android("12.0")
    java(21)
    kotlin("1.9.22")
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

**Pattern 2a**

```kotlin
KotlinAndSwiftIOSApplication {
    kotlinVersion = "1.9.22"
    swiftVersion = "1.9.22"
    iOSVersion = "14.0"
}
```

**Pattern 2b**

```kotlin
mobileApplication {
    // Templates for implementation language and target runtime
    iOS("14.0")
    kotlin("1.9.22")
    swift("14.0")
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

**Pattern 2a**

```kotlin
kotlinMobileApplication {
    kotlinVersion = "1.9.22"
    androidVersion = "12.0"
    iOS = "14.0"
}
```

**Pattern 2b**

```kotlin
mobileApplication {
    // Templates for implementation language and target runtime
    android("12.0")
    iOS("14.0")
    kotlin("1.9.22")
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

**Pattern 2a**

```kotlin
kotlinLibrary {
    kotlinVersion = "1.9.22"
    jvmVersion = "21"
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
