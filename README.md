# AndroidDebugLoggingIssue
Example project when `--debug` causes Android project configuration to fail

This succeeds:
```
./gradlew :app:assembleDebug
```

This fails:
```
./gradlew clean && ./gradlew :app:assembleDebug --debug
```
(nb: `./gradlew clean :app:assembleDebug` does not always fail)

The failure looks like this:
```
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter] FAILURE: Build failed with an exception.
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter] 
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter] * What went wrong:
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter] A problem occurred configuring project ':library'.
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter] > Could not resolve all files for configuration ':library:debugUnitTestCompileClasspath'.
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter]    > Failed to transform file 'javalibrary.jar' to match attributes {artifactType=processed-jar} using transform IdentityTransform
12:24:40.914 [ERROR] [org.gradle.internal.buildevents.BuildExceptionReporter]       > Transform output file /home/tony/workspace/temp/DebugLogging/javalibrary/build/libs/javalibrary.jar does not exist.
```
This doesn't make any sense, since there's no reason to try to resolve `:library:debugUnitTestCompileClasspath` when I'm only requesting `debug`.

Reproducing this problem seemed to require the following steps:
1. An Android library project.
1. This Android library project should have the [Android Groovy plugin](https://github.com/groovy/groovy-android-gradle-plugin) applied (use-case: Spock tests)
   1. There must be at least one Groovy class in `android-lib/src/test/groovy` to trigger the issue.
1. This Android library project must have one project dependency on a plan `java-library` proejct.
