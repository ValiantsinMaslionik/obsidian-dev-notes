#arch/design_patterns

---

> A design pattern is a proven technique that we can use to solve a specific problem.

Applying design patterns effectively will improve your designs, whether designing a small component or a whole system. However, be careful; throwing patterns into the mix just to use them can lead to the opposite result: over-engineering. Instead, **aim to write the least amount of readable code that solves your issue or automates your process.**

# Anti-patterns and code smells

**Anti-patterns** are proven bad architectural practices, while **code smells** are tips about possible flawed design.

## Anti-patterns

An **anti-pattern** is the opposite of a design pattern: it is a proven flawed technique that will most likely cause you trouble and cost you time and money (and probably give you headaches). An anti-pattern is a pattern that seems a good idea and seems to be the solution you were looking for, but it causes more harm than good. Some anti-patterns started as legitimate design patterns and were labeled anti-patterns later. Sometimes, it is a matter of opinion, and sometimes the classification can be influenced by the programming language or technologies. Let’s begin with an example. We will explore some other anti-patterns throughout the book. 6 Introduction 

### God class

A **God class** is a class that handles too many things. Typically, this class serves as a central entity that many other classes inherit or use within the application. It is the class that knows and manages everything in the system; it is the class. On the other hand, it is also the class that nobody wants to update, which breaks the application every time somebody touches it: it is an evil class. The best way to fix this is to segregate responsibilities and allocate them to multiple classes rather than concentrating them in a single class.

## Code smells

A **code smell** is an indicator of a possible problem. It points to areas of your design that could benefit from a redesign. By “code smell,” we mean “code that stinks” or “code that does not smell right.” It is important to note that a code smell only indicates the possibility of a problem; it does not mean a problem exists. Code smells are usually good indicators, so it is worth analyzing your software’s “smelly” parts. An excellent example is when a method requires many comments to explain its logic. That often means that the code could be split into smaller methods with proper names, leading to more readable code and allowing you to get rid of those pesky comments. Another note about comments is that they don’t evolve, so what often happens is that the code described by a comment changes but the comment remains the same. That leaves a false or obsolete description of a block of code that can lead a developer astray. The same is also true with method names. Sometimes, the method’s name and body tell a different story, leading to the same issues.

### Control Freak

An excellent example of a code smell is using the `new` keyword. This indicates a hardcoded dependency where the creator controls the new object and its lifetime. This is also known as the **Control Freak** anti-pattern, but I prefer to box it as a code smell instead of an anti-pattern since the `new` keyword is not intrinsically wrong. 

### Long methods

The **long methods** code smell is when a method extends to more than a certain number of lines of code. For most teams, 10 to 15 lines is enough to fall into this specific case. That is a good indicator that you should think about that method differently. Having comments that separate multiple code blocks is a good indicator of a method that may be too long.

Here are a few examples of what the case might be:
- The method contains complex logic intertwined in multiple conditional statements.
- The method contains a big switch block.
- The method does too many things.
- The method contains duplications of code.

To fix this, you could do the following:
- Extract one or more private methods.
- Extract some code to new classes.
- Reuse the code from external classes.
- If you have a lot of conditional statements or a huge switch block, you could leverage a design pattern such as the Chain of Responsibility, Behavioral Patterns, Mediator and CQS Patterns.

