## Software build outputs

A build output is defined by several aspects:

- Where is the output intended to be used?
  - Which "runtime" will it run in? Which version?
- Which software features does the output include?
  - Which optional features are enabled?
- Which development features does the output include?
  - Is the output signed? Obfuscated? Minimized? Optimized? Instrumented for profiling?

Just like the [software type](declaring-software-type.md), all of these aspects are all important things for a developer to decide on.

Cross-product, similar to what is done now:

```kotlin
androidApplication {
    buildTypes {
        development {
            // Some build settings
        }
        ci {
            // Some build settings
        }
    }
    editions {
        free {
            dependencies { }
        }
        paid {
          dependencies { }
        }
    }
    targetDevices {
        arm64 {
          dependencies { }
        }
        x64 {
          dependencies { }
        }
    }
    variants {
        common { 
            dependencies { }
        }
        variant(buildType = release, edition = paid) {
          // Some settings
        }
        variant(buildType = development, edition = free, target = x64) {
            // Some settings
        }
    }
}


```