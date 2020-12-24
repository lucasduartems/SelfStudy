# Gradle

## Über jar

The task below can be added to the `build.gradle` script to generate a “fat” `.jar` file &ndash; the *über* `.jar` &ndash; containing all the dependencies:

```gradle
jar {
    manifest {
        attributes "Main-Class": "com.example.Application"
    }
    from {
        configurations.compile.collect { it.isDirectory() ? it : zipTree(it) }
    }
}
```

## References
[Creating a Fat Jar in Gradle](https://www.baeldung.com/gradle-fat-jar)
