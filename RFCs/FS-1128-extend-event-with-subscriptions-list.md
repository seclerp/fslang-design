# F# RFC FS-1128 - Extend `Event` type with subscribers information

The design suggestion [Expose GetInvocationList from F# event](https://github.com/fsharp/fslang-suggestions/issues/938) has been marked "approved in principle".

This RFC covers the detailed proposal for this suggestion.

- [x] [Suggestion](https://github.com/fsharp/fslang-suggestions/issues/938)
- [x] Approved in principle

# Summary

The proposal is to add ability to retreive information about event invokation subscribers.

# Motivation

Currently, to have arbitary logic to happen when event has subscribers is impossible without reflection. The same problem exists for 

Reflection-based approach introduces 2 problems:
1. Performance costs
1. Dependency on internal logic makes code fragile and unstable

One of real-world cases could be found in the [suggestion](https://github.com/fsharp/fslang-suggestions/issues/938#issue-754468527).

# Detailed design

Two properties those solve problems described above could be implemented:

```fsharp
// TODO
```

.NET delegate type that is used as an implementation for the `Event` type has method `GetInvocationList()` that expose a list of subscribers.


# Drawbacks

- Additional internal implementation info is leaking outside `Event` type
- Exposing subscribers info leads to additional potential effort of implementing it on different platforms outside of .NET scope (i.e. JS and Python targets via Fable)

# Alternatives

Subscribers info could be obtained by reflection from the `multicast` internal field of type [`MulticastDelegate`](https://docs.microsoft.com/en-us/dotnet/api/system.multicastdelegate?view=net-6.0):
```fsharp
static let multicastFiled =
    typeof<Event<EventHandler, EventArgs>>.GetField("multicast", BindingFlags.NonPublic ||| BindingFlags.Instance)

let getDelegateFromEvent (event : Event<EventHandler, EventArgs>) =
    multicastFiled.GetValue event :?> EventHandler
```

This way of requesting subscribers info is .NET-dependent and will not work for other language implementations.

# Compatibility

Please address all necessary compatibility questions:

* **Is this a breaking change?**
    
    This is not a breaking change.

* **What happens when previous versions of the F# compiler encounter this design addition as source code?**

    Nothing. No changes to syntax and/or no additional constructs are introduced.

* **What happens when previous versions of the F# compiler encounter this design addition in compiled binaries?**

    Nothing.

* **If this is a change or extension to FSharp.Core, what happens when previous versions of the F# compiler encounter this construct?**

    Since no additional changes in the language are required, any version of F# compiler could be used with the source code of a new properties.

# Pragmatics

## Diagnostics

Please list the reasonable expectations for diagnostics for misuse of this feature.

## Tooling

No additional tooling required.

## Performance

Performance of this addition only depends on the implementation of "invokable" code for a target language (CIL/JS/Python). In the case of .NET, [`MulticastDelegate`](https://docs.microsoft.com/en-us/dotnet/api/system.multicastdelegate?view=net-6.0) already has all necessary properties exposed and will not affect performance.

## Culture-aware formatting/parsing

Does the proposed RFC interact with culture-aware formatting and parsing of numbers, dates and currencies? For example, if the RFC includes plaintext outputs, are these outputs specified to be culture-invariant or current-culture.

# Unresolved questions

- Should we only expose `HasSubscribers` property or `GetSubscribersList` is also required?
