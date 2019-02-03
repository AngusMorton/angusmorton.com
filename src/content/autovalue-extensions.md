---
layout: post
title: "Autovalue Extensions"
author: Angus
tags: ["android"]
date: "2018-05-06T23:46:37.121Z"
draft: false
---

[AutoValue](https://github.com/google/auto) is a tool that lets you remove a lot of the boilerplate associated with writing value types in Java. It does this by using an annotation processor to generate a well-behaved, immutable implementation of an abstract class.

So, given the abstract class `Foo`, AutoValue will then generate the implementation of the class called `AutoValue_Foo`. 
```
@AutoValue abstract class Foo {
  public abstract String bar();

  public static Foo create() {
    return new AutoValue_Foo();
  } 
}

final class AutoValue_Foo extends Foo {
  AutoValue_Foo() {...}
  public String bar() {...}
  @Override public String toString() {...}
  @Override public boolean equals(Object o) {...}
  @Override public int hashcode() {...}
}
```

So there are some immediately obvious advantages to using AutoValue;

 * No runtime cost (since it's all compile time code generation).
 * The generated class is immutable.
 * No boilerplate equals()/hashcode()/toString() method implementations.

However, AutoValue alone is insufficient for Android development because:

 * AutoValue_Foo can't be Parcelable.
 * AutoValue_Foo can't be deserialized automatically by Moshi/Gson (because the class is immutable).

### Enter AutoValue Extensions

AutoValue 1.2 added an Extensibility API that allows you to add custom behavior, like Parcelable, to an AutoValue class.

##### Parcelable AutoValue Extension
First we define our AutoValue class `Foo`. Note that our class `Foo` implements `Parcelable`. This tells the Parcelable Extension that it is applicable to this class and it should participate in code generation. 
```
@AutoValue abstract class Foo implements Parcelable {
  public abstract String bar();
}
```

The AutoValue annotation processor will generate a subclass called `$AutoValue_Foo`, which contains all of the normal AutoValue code. Note that it is designated as `abstract` because the Parcelable Extension still needs to fulfill the `Parcelable` interface.
```
abstract class $AutoValue_Foo extends Foo {
  $AutoValue_Foo() {...}
  public String bar() {...}
  @Override public String toString() {...}
  @Override public boolean equals(Object o) {...}
  @Override public int hashcode() {...}
}
```

The ParcelableExtension will then generate `AutoValue_Foo` that extends `$AutoValue_Foo`. This lets the Parcelable Extension implement the Parcelable interface properly.
```
final class AutoValue_Foo extends $AutoValue_Foo {
  @Override public int describeContents() {...}
  @Override public void writeToParcel(Parcel out, int flags) {...}
  public static final Parcelable.Creator<AutoValue_Foo> CREATOR = ...
}
```

So the full class hierarchy looks like:

```
    Foo
     ^
$AutoValue_Foo
     ^
 AutoValue_Foo
```

Also note that the final generated class is *always* called `AutoValue_Foo`, which means that nothing in the consuming code needs to change!

### TL;DR
AutoValue 1.3 has extensions, they are a powerful way to reduce the boilerplate code associated with value types. Use them!

-------
### See
 * [Extensibility API Issue]( https://github.com/google/auto/issues/202)
 * [Extensibility API Pull Request]( https://github.com/google/auto/pull/237)
 * [AutoValue Moshi Extension](https://github.com/rharter/auto-value-moshi)
 * [AutoValue Parcelable Extension](https://github.com/rharter/auto-value-parcel)
 * [Source code: AutoValueExtension.java](https://github.com/google/auto/blob/master/value/src/main/java/com/google/auto/value/extension/AutoValueExtension.java)