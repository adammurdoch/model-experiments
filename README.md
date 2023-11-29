# Model Experiments

Some mock-ups of potential Gradle software definition models.

There are two basic patterns:
- Targets are explicitly declared first and then configured separately
- Declaration and configuration are combined

## Declare targets first and configure using selectors

A Java library with multiple targets:

```kotlin
javaLibrary {
    javaVersions = listOf(11, 17, 21)
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
        // Using `java(20) { }` here would be an error as this is not declared as a target
    }
    // Using `javaVersions = x` here would be an error
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
        // Using `java(20) { }` here would be an error as this is not declared as a target
    }
    // Using `javaVersions = x` here would be an error
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

A KMP library:

```kotlin
kotlinLibrary {
    targets = listOf(jvm(21), macOsArm64(), android(24), wasm("1.1"))
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

A JVM Kotlin library:

```kotlin
kotlinLibrary {
    targets = listOf(jvm(21))
    targets {
        common {
            // What does this mean? Should it be an error?
        }
        jvm(21) {
            // Or should this one be an error?
        }
    }
}
```

## Combined declaration and configuration

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
        java(17) // declares Java 17 as a target
        java(21) // declares Java 21 as a target
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
        jvm(21) {
            // ...
        }
        macOsArm64 { 
            // ...
        }
        android(24) {
            // ...
        }
        wasm("1.1") {
            // ...
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
        }
        jvm(21) {
            // Or should this one be an error?
        }
    }
}
```
