Yea, there's no reason for this error to exist other than enforcing that you properly clean up your components. We could just as well ignore the value. I think that we'll likely make this a warning or at least make sure that it doesn't break reconciliation if it throws.

I would like to deprecate isMounted(). It's only really used as an escape to by-pass this error. The error is there to help you. If the easiest way to avoid the error is to add this isMounted() check, then it circumvents the whole purpose of having the error in the first place. You're not fixing the root problem. And as we all know, we tend to take shortcuts whenever we can. In practice, these go unfixed.

There are three separate scenarios in play here:

## 1.Sometimes asynchronously and some times synchronously calling some method that might set state.
This is generally a really bad idea. This is a race condition that will act differently depending on the timing of your system, so testing won't reveal scenarios that an end user will hit. If you write in a completely immutable style, it's not as bad, but still considered bad practice.

If you have cached values, that you would like to use instead of causing two renders, you can at least build a separate synchronous API for that. E.g. in getInitialState: if (promise.hasCachedValue) return { value: promise.cachedValue }; It's better than violating the expectations of a Promise or async callback.

Additionally, in the current server rendering model, it's best practice to trigger async side-effects in componentDidMount since that will only fire on the client.

However, this is mostly a non-issue if (3) is solved since setState is currently still allowed in componentWillMount.

## 2.setState in componentWillUnmount as a way to unify resetting values and clearing asynchronous callbacks.
This pattern seems a bit unnecessary to me. Unmounting and and reaching completion is two different concepts and are usually different code paths. Even if they do the same thing, it might be worth separating them to make refactoring easier when one changes.

I don't see this as a major issue that we even need to error in production, we could warn in dev. It doesn't suffer from the same consequences as (3). So we could even drop the warning.

However, the question is: Is it more commonly an mistake or a legit pattern? If it's a legit pattern, then we should not warn since warnings are meant to be cleaned up. Excessive warnings creates apathy. However, even if it is legit, should we warn anyway because the majority case is a mistake? I'm not sure. I could go either way. I don't think that this is a common mistake but not sure.

## 3.Calling setState when a component is completely unmounted.
This is a strong indication that an asynchronous callback isn't being properly cleaned up. Unfortunately, mainstream JS APIs makes it very easy to avoid cleaning up hanging asynchronous callbacks.

One callback isn't a big deal. However, that callback hangs on to objects and intermediate callbacks, promises and subscriptions. If alot of your components do this, you will quickly run into memory issues.

This is a case of death by a thousand cuts. There is always a reason to avoid the complexity of cleanup logic, so you end up convincing yourself that it's ok for your special case. Then you run into GC and memory issues, and blame React for being slow. This is especially critical on mobile devices where memory issues can cause a browser to crash.

There are two types of callbacks. Infinite (or very long living) callbacks, such as event handlers that fire multiple times. There are also single fire callbacks such as Promises.

Promises are not as bad since they only resolve once before they can be cleaned up. However, they also live a long time. E.g. data requests can sometimes have very long timeouts to handle low-latency/bandwidth environments. The example above of a 30 second timeout is also an example of a long living promise. If your components frequently unmount and remount, you'll accumulate a lot of hanging promises and finally crash.

Therefore, it really bothers me that the JS community has adopted Promises at scale since it doesn't come with a cleanup mechanism. However, from a pragmatic point of view, we should probably allow this pattern to work since Promises are so common and make it very difficult to do clean up. You've been warned.

The big issue is that we can't really tell if the callback is coming from a Promise or a long living event. Failure to cleanup a long living event is a very common and dangerous mistake. So, how do we prevent that mistake from happening?

Even if the current callstack is coming from a Promise, that's no guarantee. That Promise could've fired a persistent event subscription that called setState. E.g. a Flux store.

Ultimately, I think that the solution is to build support for Promises in state. That way we can easily make an exception and ignore the warning if the Promise resolves late.

We should also build in support for Observables that make it easy to get cleanup for free.

We could easily ignore these setState calls but we'd need another way to ensure that you're not making common dangerous mistakes.