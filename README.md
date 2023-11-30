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

    // Configure the targets using various selectors
    targets {

        // Common settings for all targets
        common {
            dependencies {
                api("xyz")
                implementation(projects.otherLib)
            }
        }

        // Additional settings for Java 11
        java(11) {
            dependencies {
                implementation("some-backport")
            }
        }

        // Additional setting for Java 17
        java(17) {
            dependencies {
                implementation("some-other-backport")
            }
        }

        // No additional settings for Java 21
        
        // Using `java(20) { }` here would be an error as Java 20 is not declared as a target
    }
}
```

The same library with the targets defined by a convention:

```kotlin
javaLibrary {
    targets {

        // Common settings for all targets
        common {
            dependencies { }
        }

        // Settings for Java 11
        java(11) {
            dependencies { }
        }

        // Settings for Java 17
        java(17) {
            dependencies { }
        }

        // No additional settings for Java 21

        // Using `java(20) { }` here would be an error as Java 20 is not declared as a target
    }
}
```

A Java library with one target. This is the most common scenario by far, and where developers will start.

```kotlin
javaLibrary {
    // Declare the target Java versions
    javaVersions = listOf(17)
    
    targets {
        common {
            // What does this mean? Should it be an error?
            dependencies { }
        }
        java(17) {
            // Or should this one be an error?
            dependencies { }
        }
        
        // Using `java(20) { }` here would be an error as this is not declared as a target
    }
}
```

A Kotlin library with multiple targets:

```kotlin
kotlinLibrary {

    // Declare the Kotlin targets 
    kotlinTargets = listOf(
        jvm(17),
        jvm(21),
        macosX86("14.0"),
        macosArm64("14.0"),
        android(21),
        android(24),
        wasm("1.1")
    )

    // Configure the targets using various selectors
    targets {

        // Common settings for all targets
        common {
            dependencies { 
                api("xyz")
            }
        }

        // Common settings for all native targets
        native {
            dependencies { }
        }

        // Common settings for all macOS targets
        macos {
            dependencies { }
        }

        // Common settings for all JVM targets
        jvm {
            dependencies { }
        }

        // Settings for Java 21
        jvm(21) {
            // Settings for Java 21
        }
        
        // No additional configuration for the other targets
    }
}
```

A Kotlin/JVM library:

```kotlin
kotlinLibrary {
    
    kotlinTargets = listOf(jvm(21))
    
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

        // Common settings for all targets
        common {
            dependencies {
                api("xyz")
                implementation(projects.otherLib)
            }
        }

        // Declares Java 11 as a target
        java(11) {
            dependencies {
                implementation("some-backport")
            }
        }

        // Declares Java 17 as a target            
        java(17) {
            dependencies {
                implementation("some-other-backport")
            }
        }

        // Declares Java 21 as a target, with no additional configuration
        java(21) 
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

        // Common settings for all targets
        common {
            dependencies { 
                api("xyz")
            }
        }
        // Common settings for native targets
        native {
            dependencies { }
        }

        // Common settings for macOS targets
        macos {
            dependencies { }
        }

        // Common settings for all JVM targets
        jvm {
            dependencies { }
        }

        // Declares Java 17 as a target with no additional configuration
        jvm(17)

        // Declares Java 21 as a target
        jvm(21) {
        }

        // Declares macOS 14.0 + intel as a target
        macOsX64("14.0") { 
        }

        // Declares macOS 14.0 + Apple Silicon as a target
        macOsArm64("14.0") { 
        }

        // Declares Android 21 as a target
        android(21) {
        }

        // Declares Android 24 as a target
        android(24) {
        }

        // Declares WASM 1.1 on the browser as a target
        wasm("1.1") {
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

Inline the `targets` block, as it doesn't have much meaning for developers:

A Java library with multiple targets:

```kotlin
javaLibrary {
    common {
        dependencies { }
    }
    java(11) {
        dependencies { }
    }
    java(17)
    java(21)
}
```

A Java library with a single target:

```kotlin
javaLibrary {
    java(21) {
        dependencies { }
    }
}
```

A KMP library with multiple targets:

```kotlin
kotlinLibrary {
    common {
        dependencies { }
    }
    native {
        dependencies { }
    }
    macos {
        dependencies { }
    }
    jvm {
        dependencies { }
    }
    jvm(17)
    jvm(21) {
        dependencies { }
    }
    macosX64("14.0")
    macosArm64("14.0")
    android(24)
}
```

A Kotlin/JVM library with a single target:

```kotlin
kotlinLibrary {
    jvm(17) {
        dependencies { }
    }
}
```
