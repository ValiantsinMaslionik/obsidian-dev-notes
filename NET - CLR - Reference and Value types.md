#NET/Platform 

---

There are two categories of memory: **stack** and **heap** memory.With modern OSes, the stack and heap can be anywhere in physical or virtual memory.

**Stack** memory is faster to work with (because it;s managed directly by the CPU and because it uses FIFO mechanism, it's more likely to have the data in its L1 or L2 cache) but limited in size, while **heap** memory is slower but much more pleintful.

Keyword|Type|Description
--|--|--
class|reference&nbsp;type|The memory for the object itself is allocated on the heap, and only the memory address of the object (and a little verhead) is stored on the stack
struct|value type|The memory for the object itself is allocated on the stack

>![[zICO - Warning - 16.png]] If a **struct** uses field types that are not of the **struct** type, then those fields will be stored on the heap, meaning the data for that object is stored in both the stack and the heap!

>![[zICO - Exclamation - 16.png]] You cannot inherit from a **struct**

>![[zICO - Warning - 16.png]] If the total bytes used by all the fields in your type is **16 bytes or less**, your type only uses struct types for its fields, and you will never want to derive from your type, the Microsoft recommends that you use **struct**