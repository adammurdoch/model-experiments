# Model Experiments

Some mock-ups of potential Gradle software definition models.

There are two basic patterns:
1. Targets are explicitly declared first and then configured separately
2. Declaration and configuration are combined

## Pattern 1: Separate declaration and configuration

A Java library with multiple targets:

```kotlin
javaLibrary {
    // Declare the target Java versions
    javaVersions = listOf(11, 17, 21)

    // Configure the targets     
    targets {
        common {
            // Common settings for all targets
            dependencies {
                api("xyz")
                implementation(projects.otherLib)
            }
        }
        java(11) {
            // Settings for Java 11
            dependencies {
                implementation("some-backport")
            }
        }
        // Using `java(20) { }` here would be an error as Java 20 is not declared as a target
    }
}
```

The same library with the targets defined by a convention:

```kotlin
javaLibrary {
    targets {
        common {
            // Common settings for all targets
        }
        java(11) {
            // Settings for Java 11
        }
        // Using `java(20) { }` here would be an error as Java 20 is not declared as a target
    }
}
```

A Java library with one target (the most common by far, and where developers will start)

```kotlin
javaLibrary {
    javaVersions = listOf(17)
    targets {
        common {
            // What does this mean? Should it be an error?
        }
        java(17) {
            // Or should this one be an error?
        }
        // Using `java(20) { }` here would be an error as this is not declared as a target
    }
}
```

A Kotlin library with multiple targets:

```kotlin
kotlinLibrary {
    // Declare the Kotlin targets 
    targets = listOf(jvm(21), macOsArm64("14.0"), android(24), wasm("1.1"))
    
    // Configure each target
    targets {
        common {
            // Common settings for all targets
            dependencies { 
                api("xyz")
            }
        }
        jvm(21) {
            // ...
        }
    }
}
```

A Kotlin/JVM library:

```kotlin
kotlinLibrary {
    targets = listOf(jvm(21))
    targets {
        common {
            // What does this mean? Should it be an error?
            dependencies { }
        }
        jvm(21) {
            // Or should this one be an error?
            dependencies { }
        }
    }
}
```

## Pattern 2: Combined declaration and configuration

A Java library with multiple targets:

```kotlin
javaLibrary {
    targets {
        common {
            // Common settings for all targets
            dependencies {
                api("xyz")
                implementation(projects.otherLib)
            }
        }
        java(11) {
            // Declares Java 11 as a target
            dependencies {
                implementation("some-backport")
            }
        }
        java(17) // Declares Java 17 as a target
        java(21) // Declares Java 21 as a target
    }
}
```

A Java library with one target

```kotlin
javaLibrary {
    targets {
        common {
            // What does this mean? Should it be an error?
            dependencies { }
        }
        java(17) {
            // Declares Java 17 as a target and configures it
            dependencies { }
        }
    }
}
```

A KMP library:

```kotlin
kotlinLibrary {
    targets {
        common {
            // Common settings for all targets
            dependencies { 
                api("xyz")
            }
        }
        native {
            // Common settings for native targets
        }
        macos {
            // Common settings for macOS targets
        }
        jvm(21) {
            // Declares Java 21 as a target
        }
        macOsArm64("14.0") { 
            // Declares macOS 14.0 + Apple Silicon as a target
        }
        android(24) {
            // Declares Android 24 as a target
        }
        wasm("1.1") {
            // Declares WASM 1.1 on the browser as a target
        }
    }
}
```

A JVM Kotlin library:

```kotlin
kotlinLibrary {
    targets {
        common {
            // What does this mean? Should it be an error?
            dependencies { }
        }
        jvm(21) {
            // Or should this one be an error?
            dependencies { }
        }
    }
}
```

### Variation: remove Gradle specific concept 'target'

```kotlin
javaLibrary {
    common {
        // ...
    }
    java(11) {
        // ..
    }
    java(17)
    java(21)
}
```

```kotlin
javaLibrary {
    java(21) { 
        // ...
    }
}
```

```kotlin
kotlinLibrary {
    common { 
        // ...
    }
    jvm(17) {
        // ...
    }
    native { 
        // ...
    }
    macos { 
        // ...
    }
    macosArm64("14.0") {
        // ...
    }
    android(24) {
        // ...
    }
}
```

```kotlin
kotlinLibrary {
    jvm(17) {
        // ...
    }
}
```