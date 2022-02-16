# set-task-names-gradle-7.4

This is a repro for a regression in Gradle 7.4, where if configuration-on-demand is enabled,
`gradle.startParameter.setTaskNames()` can no longer be used in Gradle configuration code to
modify the tasks that are to be run. This worked in 7.3.3 and before.

For an example of why setTaskNames can be useful, see the [gradle-sls-packaging] plugin,
which [adds a lock-writing task via setTaskNames] when `--write-locks` is used.

Example runs:

Correct output when CoD is disabled (the task to run is forced to be `foo`):

```
$ ./gradlew bar --no-configure-on-demand

> Task :foo
Hello, foo!

BUILD SUCCESSFUL in 573ms
1 actionable task: 1 executed
```

Incorrect output when using CoD (setTaskNames is ignored):

```
$ ./gradlew bar
Configuration on demand is an incubating feature.

> Task :bar
Hello, bar!

BUILD SUCCESSFUL in 590ms
1 actionable task: 1 executed
```

[gradle-sls-packaging]: https://github.com/palantir/sls-packaging
[adds a lock-writing task via setTaskNames]: https://github.com/palantir/sls-packaging/blob/bfd2e650a807c98a00ef6d041d530ff7654984ee/gradle-sls-packaging/src/main/java/com/palantir/gradle/dist/tasks/CreateManifestTask.java#L275-L284