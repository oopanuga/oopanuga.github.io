
---
layout: post
title: Did you know that...? CSharp´s foreach
---

**IEnumerator** is a simple interface that defines a basic low level protocol to traverse a collection in a forward-only manner. The protocol is so simple:

![Image description](/images/foreach/IEnumerator.jpg)

Collections normally don't implement enumerators directly. Instead, they provide enumerators via **IEnumerable** interface:

![Image description](/images/foreach/IEnumerable.jpg)

The benefit of implementing this interface in a collection is that several consumers of the collection can enumerate it without interfering with each other. 

Enumerable can be thought of as _IEnumeratorProvider_.

So we could walk a collection like this:

![Image description](/images/foreach/WalkEnumerator.jpg)

However we have a  great syntactic sugar for Enumerable: *foreach*.

![Image description](/images/foreach/Foreach.jpg)

The compiler will convert the former foreach into the previous enumeration using the underlying Enumerator.

## There's more...

We normally will make use of the generic counterparts, **IEnumerator<T>** and **IEnumerable<T>**.
One of the main advantages of using the generic version is that IEnumerator<T> inherits from *IDisposable*. This allows us to have a collection on which every element can be disposed, like a database connection.

So let's go to the point of this post. Let's see real magic in action.

1. This is what we write:

![Image description](/images/foreach/foreach2.jpg)

2. Compiler converts this to use an using block because of IDisposable nature of IEnumerable + Compiler converts it to use underlying enumerator:

![Image description](/images/foreach/trans1.jpg)

3. Compiler converts this using block to call Dispose defined in IDisposable:

![Image description](/images/foreach/trans2.jpg)

(I know I have a compiling error but you get the idea right? :))

If we compare the first piece with the last one we can save a few lines of code, increasing developers productivity and expressing ideas in a more concise manner.

This is the magic that happens behind the scenes if we call a foreach using a disposable collection.

Keep coding!