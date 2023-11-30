# Model Experiments

Some mock-ups of potential Gradle software definition models.

One of the main questions to answer is how to structure the model for software with multiple targets.
The following examples look at two basic patterns:

1. Targets are explicitly declared first and then separately configured using various selectors.
2. Declaration and configuration are combined into a single block.

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

Selectors would be applied in a fixed order, and this might also be enforced in the grammar so that this is more explicit.

It would be an error to use a selector that does not match anything.

Here is the same library with conventions applied:

```kotlin
javaLibrary {
    
    // Targets are already defined by the convention.
    // Potentially they could be overridden here
    
    targets {

        // Common settings are already defined by the convention.
        // Potentially some project-specific conventions could be added here
        // common { }

        // Additional settings for Java 11
        java(11) {
            dependencies { }
        }

        // Additional settings for Java 17
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
        // Should common { } be allowed in this case?

        // Settings for Java 17
        java(17) {
            dependencies {
                api("xyz")
            }
        }
        
        // Using `java(20) { }` here would be an error as this is not declared as a target
    }
}
```

A Kotlin library with multiple targets:

```kotlin
kotlinLibrary {
    
    // Declare the target Kotlin version.
    // There could potentially be multiple versions (for example when we're building a Gradle plugin for different Gradle versions)
    // Keep this simple for now. Dealing with multiple dimensions will be explored later.
    kotlinVersion = "1.9.21"

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

        // Additional settings for all native targets
        native {
            dependencies { }
        }

        // Additional settings for all macOS targets
        macos {
            dependencies { }
        }

        // Additional settings for all JVM targets
        jvm {
            dependencies { }
        }

        // Additional settings for Java 21
        jvm(21) {
            dependencies { }
        }
        
        // No additional configuration for the other targets
    }
}
```

A Kotlin/JVM library:

```kotlin
kotlinLibrary {

    kotlinVersion = "1.9.21"
    kotlinTargets = listOf(jvm(21))
    
    targets {
        // Allow common { }?
        
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
    
    // The targets and selectors are combined in this block
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

As for the previous pattern, the selectors would be applied in a fixed order, and this might also be enforced in the grammar.

It would be an error to use a selector that does not match anything.

A Java library with one target

```kotlin
javaLibrary {
    targets {
        // Maybe common { }?

        // Declares Java 17 as a target and configures it
        java(17) {
            dependencies { }
        }
    }
}
```

A KMP library:

```kotlin
kotlinLibrary {
    targets {

        kotlinVersion = "1.9.21"

        // Common settings for all targets
        common {
            dependencies { 
                api("xyz")
            }
        }

        // Additional settings for native targets
        native {
            dependencies { }
        }

        // Additional settings for macOS targets
        macos {
            dependencies { }
        }

        // Additional settings for all JVM targets
        jvm {
            dependencies { }
        }

        // Declares Java 17 as a target with no additional configuration
        jvm(17)

        // Declares Java 21 as a target
        jvm(21) {
            dependencies { }
        }

        // Declares macOS 14.0 + intel as a target
        macOsX64("14.0")

        // Declares macOS 14.0 + Apple Silicon as a target
        macOsArm64("14.0")
        
        // Declares Android 21 as a target
        android(21)

        // Declares Android 24 as a target
        android(24)

        // Declares WASM 1.1 on the browser as a target
        wasm("1.1")
    }
}
```

A JVM Kotlin library:

```kotlin
kotlinLibrary {
    kotlinVersion = "1.9.21"

    targets {
        // Allow common { }?

        jvm(21) {
            dependencies { }
        }
    }
}
```

### Pattern 2 Variation 2: Don't require Gradle specific concept 'target'

With pattern 2, we could inline the `targets` block, as it doesn't have much meaning for developers:

A Java library with multiple targets:

```kotlin
javaLibrary {
    // Common settings for all targets
    common {
        dependencies { }
    }
    
    // Define targets and additional configuration
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
    // Define target and configuration
    java(21) {
        dependencies { }
    }
}
```

A KMP library with multiple targets:

```kotlin
kotlinLibrary {
    // Common settings for all targets
    kotlinVersion = "1.9.21"
    common {
        dependencies { }
    }
    
    // Targets and selectors
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
    kotlinVersion = "1.9.21"
    jvm(17) {
        dependencies { }
    }
}
```

### Pattern 2 Variation 3: Don't require Gradle specific concept 'common'

In the previous variation, the `common { }` block could be inlined, as the "common" nature of the settings is implied by making them
top level.

A Java library with multiple targets:

```kotlin
javaLibrary {
    // Dependencies for all targets
    dependencies { }
    
    // Declare targets
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
    // Dependencies for all targets
    dependencies { }

    // Declare target
    java(21)
}
```

A KMP library with multiple targets:

```kotlin
kotlinLibrary {
    // Settings for all targets
    kotlinVersion = "1.9.21"
    dependencies { }

    // Targets and selectors
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
    kotlinVersion = "1.9.21"
    dependencies { }
    jvm(17)
}
```
