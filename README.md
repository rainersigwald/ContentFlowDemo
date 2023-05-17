# Pulling arbitrary content from a referenced project

`Microsoft.Common.targets` will pull `@(Content)` items from referenced projects, but is generally structured to assume that every project has a single canonical output, which may have associated files. That's not the case for `.esproj`, which produces a bundle of JavaScript that needs to be consumed by a referencing app.

This repo shows a minimal project [`ContentProducer.customproj`](ContentProducer/ContentProducer.customproj) that doesn't even import `Microsoft.Common.targets` and a standard `dotnet new webapp` project [`WebApp.csproj`](WebApp/WebApp.csproj) that has a `ProjectReference` to it.

In order to flow with the current targets, both sides of the project must participate:

1. The producer must return items from `GetCopyToOutputDirectoryItems` that are fully qualified and that define a `TargetPath` that makes sense relative to the consuming project's output directory.
2. The consumer must reference the project with additional metadata `CopyToOutputDirectory` to actually get the files copied.

In the current implementation, the consumer must also set `SkipGetTargetFrameworkProperties="true"`, but a more sophisticated set of targets in the producer like `.esproj` has should be able to avoid that.
