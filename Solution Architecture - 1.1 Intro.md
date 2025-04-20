
## Software Architecture: Definitions

#### ISO/IEC/IEEE 42010

Fundamental concept or **properties of a system** in its environment embodied in its **elements**, **relationships**, and in the **principles of its design and evolution**.

#### SEI (Software Engineering Institute)

The software architecture of a system in the **set of structures** needed to reason about the system, which comprise **software elements**, **relations among them**, and **properties** of both.

#### Microsoft Application Architecture Guide

Software architecture is the **process of defining a structured solution** that meets all of the business, technical and operational requirements. It involves a **series of decisions** based on a wide range of factors, and each of these decisions can have considerable impact on the quality.

> The architecture of a system delivered in the context of a specific solution.

*Important Insights*

- Architecture consists of elements and relations among them
- Architecture elements have attributes (or properties)
- Elements interacts with each other via interfaces
- Architecture suppresses the element's internal details and is concerned with interfaces
- Architectural design is based on a variety of requirements

### Example

![[Pasted image 20250409232454.png]]

Most probably, **elements** here are hardware elements: web servers, machines, load balancers and so on. There are not many attributes in this diagram, but at least we can see such **properties** as auto scaling. So, there is the **auto scaling group, and this is a property of elements** included into the group. We could say that there are essentially no details about **interfaces**. 
What are the **element's internal details**? All programming languages, software frameworks that might be used to implement software deployed on a web server, on an application server **are outside of a solution architecture**.

## Solution Architecture: Definition

#### Gartner

Solution architecture is an **architectural description** of a specific **solution**. Solution architects combine guidance from different enterprise architecture view points (**business, information, and technical**), as well as from the enterprise solution architecture (ESA).

#### Wikipedia

Solution architecture is a practice of defining and describing an **architecture of a system** delivered **in the context of a specific solution** and such it may encompass description of an entire system or only its specific parts.

**Q: What is NOT true**
1) No universal definition of solution architecture exists.
2) **Architecture's primary focus is the elements internal details** **FALSE**
3) Architecture design is based on a variety of requirements
4) Architecture consists of elements and relations among them
5) A board definition of solution architecture can be as follows: The architecture of a system delivered in the context of a specific solution





