---
title: Target Definitions
---

Inside a `Build` class, you can define your build steps as `Target` properties. The implementation for a build step is provided as a lambda function through the `Executes` method: 

=== "Regular Targets"

    ```csharp title="Build.cs"
    class Build : NukeBuild
    {
        public static int Main() => Execute<Build>();

        Target MyTarget => _ => _
            .Executes(() =>
            {
                Console.WriteLine("Hello!");
            });
    }
    ```

=== "Async Targets"

    ```csharp title="Build.cs"
    class Build : NukeBuild
    {
        public static int Main() => Execute<Build>();

        Target MyTarget => _ => _
            .Executes(async () =>
            {
                await Console.Out.WriteLineAsync("Hello!");
            });
    }
    ```

    !!! caution
        Async targets are just a convenience feature that allows you using async APIs in a straightforward way. Behind the scenes, they are still run synchronously.

## Dependencies

Specifying dependencies is essential to let targets run in a meaningful and predictable order. There are 3 different types of dependencies, each of them can be defined from both directions.

=== "Execution Dependencies"

    <!--Execution dependencies define that one target _must_ run before another target (unless it is skipped):-->

    Define that target `A` must run before target `B` unless `A` is skipped:

    ```csharp title="Build.cs" hl_lines="4 8"
    class Build : NukeBuild
    {
        Target A => _ => _
            .DependentFor(B)      // Choose this...
            .Executes(() => { });

        Target B => _ => _
            .DependsOn(A)         // ...or this!
            .Executes(() => { });
    }
    ```

=== "Ordering Dependencies"

    <!--Ordering dependencies define that one target _should_ run before/after another target _if_ they're both scheduled:-->

    Define that target `A` runs before target `B` if both are scheduled:

    ```csharp title="Build.cs" hl_lines="4 8"
    class Build : NukeBuild
    {
        Target A => _ => _
            .Before(B)            // Choose this...
            .Executes(() => { });

        Target B => _ => _
            .After(A)             // ...or this!
            .Executes(() => { });
    }
    ```

=== "Trigger Dependencies"

    <!--Trigger dependencies define that one target _causes_ another target to run once it has succeeded:-->

    Define that target `A` invokes target `B` once it completes:


    ```csharp title="Build.cs" hl_lines="4 8"
    class Build : NukeBuild
    {
        Target A => _ => _
            .Triggers(B)          // Choose this...
            .Executes(() => { });

        Target B => _ => _
            .TriggeredBy(A)       // ...or this!
            .Executes(() => { });
    }
    ```

!!! tip
    When choosing a direction, you should ask yourself which target should know about the existence of the other. For instance, should a `Release` target _trigger_ a `Tweet` target? Or should a `Tweet` target _be triggered_ by a `Release` target?

!!! warning

    Dependencies between targets are ONLY defined between the individual targets and NOT through their positions in a dependency call. The following examples illustrate the difference between the **partial and total order** of targets:


    === "Partial Order"

        The execution is nondeterministic between `A->B->C` and `B->A->C`. This isn't necessarily problematic, but something to be aware of. In particular, it allows different targets to run in parallel (currently only in compatible CI/CD environments).

        ```csharp title="Build.cs" hl_lines="10"
        class Build : NukeBuild
        {
            Target A => _ => _
                .Executes(() => { });

            Target B => _ => _
                .Executes(() => { });

            Target C => _ => _
                .DependsOn(A, B)
                .Executes(() => { });
        }
        ```


    === "Total Order"

        The execution is always deterministic with `A->B->C`.

        ```csharp title="Build.cs" hl_lines="7 11"
        class Build : NukeBuild
        {
            Target A => _ => _
                .Executes(() => { });

            Target B => _ => _
                .DependsOn(A)
                .Executes(() => { });

            Target C => _ => _
                .DependsOn(B)
                .Executes(() => { });
        }
        ```

## Conditional Execution

Apart from [skipping targets manually](../getting-started/execution.md#skipping-targets), you can also programmatically decide whether a target should be skipped. Depending on the use-case, you can choose between dynamic and static conditions.

=== "Dynamic Condition"

    Define a condition that is checked right before target `B` executes:

    ```csharp title="Build.cs" hl_lines="10"
    class Build : NukeBuild
    {
        readonly List<string> Data = new();

        Target A => _ => _
            .Executes(() => { /* Populate Data */ });

        Target B => _ => _
            .DependsOn(A)
            .OnlyWhenDynamic(() => Data.Any())
            .Executes(() => { });
    }
    ```

=== "Static Conditions"

    Define a condition that is checked before target `A` and `B` execute:

    ```csharp title="Build.cs" hl_lines="7-9"
    class Build : NukeBuild
    {
        Target A => _ => _
            .Executes(() => { });

        Target B => _ => _
            .OnlyWhenStatic(() => IsLocalBuild)
            // By default, dependencies are skipped
            .WhenSkipped(DependencyBehavior.Execute)
            .DependsOn(A)
            .Executes(() => { });
    }
    ```

!!! tip
    When a condition is not met, the skip reason is created from the boolean expression. For more complex conditions, you can extract the logic into a separate method or property to make the message more readable.

## Requirements

You can define target requirements that are checked right at the beginning of the build execution before any other targets are executed:

```csharp title="Build.cs" hl_lines="4"
class Build : NukeBuild
{
    Target A => _ => _
        .Requires(() => IsServerBuild)
        .Executes(() => { });
}
```

!!! note
    Target requirements are an important aspect to achieve a [fail-fast behavior](https://en.wikipedia.org/wiki/Fail-fast). Preceding targets won't waste any execution time only to discover that a condition that was known right from the beginning was not met.

!!! tip
    When a requirement is not met, the exception message is created from the boolean expression. For more complex requirements, you can extract the logic into a separate method or property to make the message more readable.

## Failure Handling

Not every failing target should completely stop the build. Targets that are not essential can allow to continue the execution for other targets are important to run even if another target has failed. For these use-cases, you can configure the failure handling. 

=== "Proceeded Execution"

    Define that execution continues after target `A` throws:

    ```csharp title="Build.cs" hl_lines="4"
    class Build : NukeBuild
    {
        Target A => _ => _
            .ProceedAfterFailure()
            .Executes(() =>
            {
                Assert.Fail("error");
            });
    
        Target B => _ => _
            .DependsOn(A)
            .Executes(() => { });
    }
    ```

=== "Assured Execution"

    Define that target `B` executes even if another target fails:

    ```csharp title="Build.cs" hl_lines="10"
    class Build : NukeBuild
    {
        Target A => _ => _
            .Executes(() =>
            {
                Assert.Fail("error");
            });
    
        Target B => _ => _
            .AssuredAfterFailure()
            .DependsOn(A)
            .Executes(() => { });
    }
    ```

## Unlisting Targets

It is good practice to follow the [single-responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) when implementing targets. However, you may not want to expose every target through the [build help text](../getting-started/execution.md#help-text). For cases like this, you can un-list a target:

```csharp title="Build.cs" hl_lines="4"
class Build : NukeBuild
{
    Target A => _ => _
        .Unlisted()
        .Executes(() => { });
}
```
