#NET/Platform

---

## Links

[How do delegates work](zDOC_NET_How-do-NET-delegates-work.mhtml)

## Calling methods using delegates

```cs
public int MethodIWantToCall(string input)
{
	// ...
}

delegate int DelegateWithMatchingSignature(string s);

var d = new DelegateWithMatchingSignature(p1.MethodIWantToCall);
int answer = d("Frog");
```

> Delegates have built-in support for asynchronous opertaions that run om a different thread, and that can provide improved responsiveness.

## Predefined delegates  to use with events

```cs
public delegate void EventHandler(object sender, EventArgs e);

public delegate void EventHandler<TEventArgs>(object sender, TEventArgs e);

// Example
// ...
public EventHandler Shout;
//...
private static void Harry_Shout(object sender, EventArgs e)
{
	// ...
}
// ...
harry.Shout = Harry_Shout;
```

>![[zICO - Warning - 16.png]] Instead of the **=** assignment, we could have used the **+=** (or **-=**) operator so we could add more (or remove) methods to the same delegate field. When the delegate is called, all the assigned methods are called, although you have no control over the order that they are called in.
