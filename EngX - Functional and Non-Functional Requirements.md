
Requirements that are well thought through and clearly documented are essential to any successful software engineering project. There are two main different types of system requirements that should be gathered by those working on software projects. System requirements can be categorized as either functional requirements or non functional requirements. Understanding the difference between the two helps to ensure that developers will deliver a product that performs as expected. Research shows that 68% of IT projects fail. One of the main reasons for this is poor definition of requirements at the start. Understanding the difference between functional and non functional requirements helps in avoiding this. Understanding requirements means knowing what functional requirements and non functional requirements are and how to define them.

## What are functional requirements in software engineering

**Something the system must do.**

If the system does not meet a functional requirement it will fail. This is because it will not be able to achieve something it must do to operate properly. The functional requirement concept can also be understood through reviewing the system in terms of inputs and outputs. Functional requirements specify what the system must do in response to different inputs and what it must output.

#### Functional requirements examples

- Specifications of what the system must do
- Business rules that must be met
- Steps that the system must take in authentication
- Details of what must be tracked in the system
- The reporting requirements of the system
- Specifics relating to legal or regulatory compliance
- Outlines the levels of user and their authorization
- Details of how transactions must occur
- The external interfaces of the system

Thinking about some more specific functional requirements examples, these might include:
- The system capability to report on the number of transactions that were processed correctly.
- What the system does when a user selects a certain button where they are taken next.
- What happens in the event of an attack on the system.
- The way that a user is authenticated when he or she logs in to the system.
- What are non functional requirements in software engineering?

#### Non functional requirements in software engineering:

**Requirements that describe how the system works.**

Non functional requirements are focused on how the system goes about delivering a specific function. At first glance they might be seen as less important than functional requirements but both have a part to play in a good system. Non functional requirements do not have an impact on the functionality of the system but they do impact on how it will perform. In short, non functional requirements are all about system usability. If non functional requirements are not met, users may become frustrated with how the system works and go elsewhere. Meeting at least some non functional requirements is important in a well performing system.

#### Non functional requirements examples


Speed – how fast the system performs certain activities.
Availability – for how much of the time the system is available e.g. does it operate overnight, or every day of the year, or not.
Capacity – what the limits are of what the system is able to handle.
Reliability – how dependable the system is.
Usability – how easy the system is to use for the customer or end user. 

Exploring this concept in greater detail, non functional requirements examples might include:

- The time taken for a specific page to load.
- The speed within which certain requests must be processed.
- The level of availability the system should have.
- Which functions can be performed at different times, and when maintenance will be carried out.
- How many users the system can handle concurrently.
- What is the difference between functional and non functional requirements?

For some, the difference between functional and non functional requirements can be difficult to understand. The table below helps to summarize the main differences between functional and non functional requirements.

|Functional Requirements|Non Functional Requirements|
|-|-|
|Help understand the functions of the system.|Help understand the performance of the system.|
|Explain the characteristics that a system is expected to have.|Explain the way in which the product should work (how it should behave)|
|Identify what the system must or must not do.|Identify how the system should do it.|
|Will allow the system to perform, even if the non-functional requirements are not met.|The system will not work with non-functional requirements alone.|
|Ensures the system will meet user requirements.|Ensures the product meets user expectations.|
|Are essential to system operations.|May be desirable but are not always essential.|
|Straightforward to define and agree on.|Harder to define and agree on.|
|Meeting these requirements is obligatory.|Meeting these requirements is not obligatory but may be desirable.|
|Define the system or an element of it.|Outline quality attributes of the system.|
|Usually defined by the user.|**Usually defined by software engineers, developers, software architects or other technical experts**.|
|Can be documented and understood through a use case.|Can be documented and understood as a quality attribute.|

## How to write functional requirements

Functional requirements can be prepared in a number of different ways. Most commonly they are documented in a text form. However, other formats are possible. These include use cases, prototypes, models, diagrams and user stories. 

#### Text requirements

These explain in writing to the reader how the system will work.

#### Use cases

Use cases explain what will happen when users interact with the system. They deliver the requirements from the perspective of the end user of the system. Use cases can either be explained in writing or presented in a diagram. Use cases must include three different components:

1. Actors – the system users who are interacting with the system.
2. System – how the system must act in the given scenario.
3. Goals – why the user is interacting with the system.

Use cases should describe how the flow works and indicate the actors and the parts of the system used in the case. They must present initial status and preconditions to be met before the interaction can occur. They state the basic steps and outline what happens to the system at the end of the case. They also detail exceptions to the rule, answering “What if?” questions.
Use cases are really good for explaining how a system will work without a great deal of technical knowledge or understanding.

#### Prototypes

Prototypes help show how requirements should be delivered. They are helpful for people to see how the system will work. They provide a visual way for people to see if requirements will be implemented correctly.
When software prototypes work well they can sometimes go on to become **minimum viable products (MVPs)***.

> A **Minimum Viable Product (MVP)** is the simplest version of a product that includes just enough core features to satisfy early users and gather valuable feedback for future development. The goal of an MVP is to test a product idea quickly and cost-effectively, allowing teams to validate assumptions, learn from user interactions, and make informed decisions about further development. This concept is widely used in **Lean Startup methodology**, where the focus is on building, measuring, and learning iteratively to minimize risks and avoid unnecessary work. By starting small and refining based on real-world feedback, businesses can ensure they are creating something that truly meets market needs.

#### Models and diagrams

Models and diagrams help to show in a simple format how a system will work.
Sometimes it is easier to understand a requirement if it is shown in a diagram, rather than reading a complex explanation. Diagrams are a good way to see how processes within the system will work.

#### User stories

These explain the function from the perspective of the end user.

Examples include:
·   “As a customer I want to view product specifications to decide if I will buy.”
·   “As a system administrator I need to have the capability to set up new users who have different system access.”

User stories work best when acceptance criteria are also specified.

For example:
·   “When I click on the product image the system loads the product specifications.”
·   “When I set up a basic user, that user receives a verification email.”

These acceptance criteria help to measure whether the specific functional requirements have been met or not when the system is built.

#### Work Breakdown Structure (WBS)

A Work Breakdown Structure (WBS) helps define how complex functions can be subdivided into smaller system components. WBS helps explain how the different system components work and how they come together as part of the overall system. Work Breakdown Structure is also sometimes known as **“functional decomposition”** in software engineering.

The way this process works is by starting with the system at a high level and breaking it down again and again. This is done until the system can be broken down no more and each component is clearly explained.

## Recommendations

Functional requirements need to be clear for all the different people that need to understand them. This includes everyone from stakeholders in the business who are non-technical through to the developers that will do the coding. Using simple language that is jargon-free is best. It is worth remembering that people have different learning styles and understand concepts best in different ways. Some do this by reading, some by hearing and some by seeing a concept in action. This means that using text, diagrams and even prototypes can be helpful in engaging different stakeholders. In an ideal world, functional requirements would be documented first before moving onto non functional requirements. However, the reality is that in many cases these are documented at the same time as stakeholders and users work out what the system needs to do and how.

Documenting clear functional requirements and non functional requirements is important for a number of reasons, as follows:

#### Avoid project failure 
When requirements are not documented, projects tend to go wrong. What the customer wants may not be clearly understood. Usually the end result of not documenting requirements is going over budget or taking much longer than anticipated to deliver the project. In the worst case this can, and often does, lead to project failure.

#### Ensure the system works as anticipated 
How the business owners explain a system may not fully cover how it must operate. Documenting requirements to come up with a clear specification helps software engineers understand the needs of the system. Failing to clarify requirements can lead to some nasty surprises for business owners!

#### Work within budget 
At the start of projects, customers may have a lot of requirements. Some of these may be essential and others only desirable. When the functional and non functional requirements are documented they can be costed. When the customer understands the costs they can opt to keep or remove requirements according to their budget.

#### Refine scope
Defining requirements helps to refine the scope. Typically, non functional requirements tend to be cut first in doing this. This is because these are often more costly than functional requirements. However, some non functional requirements must be met to ensure a reasonable user experience.

#### Highlight additional requirements 
When requirements are documented and reviewed by software engineers these can lead to further questions about functional requirements. Ultimately, as the old saying goes, “Failing to plan is planning to fail”. This is very true for functional requirements in software engineering. If everyone understands and agrees on the requirements the software engineering project is more likely to achieve its goals.

#### The Bottom Line
Systems need both functional requirements and non functional requirements to be usable. Functional requirements define how the system must work and non functional requirements detail how it should perform. Without functional requirements the system will not work. Without at least some non functional requirements being met to a certain level, users will become frustrated. Documenting functional requirements and non functional requirements helps to avoid disappointment with a new system. It is also important in keeping the project on track, to budget and delivered on time. Documenting requirements is also more likely to lead to greater satisfaction with the end product for both business stakeholders and end users. At Enkonix we are always keen on the best quality for our customers. Tell us about your project and we’ll find the best options for it.


## FAQ section

**Q: What is the difference between functional and non functional requirements?**
A: Functional requirements explain how the system must work, while non functional requirements explain how the system should perform.

**Q: Does my system need non functional requirements?**
A: To simply operate, the system does not need non functional requirements to be met. To work in a manner that the user expects, it will likely need some non functional requirements to be met.

**Q: Is performance a functional requirement?**
A: Performance is not a functional requirement. It is considered a quality attribute, so it is a non functional requirement.

**Q: Why should I document requirements?**
A: Documenting requirements ensures everyone is clear on what the system must do and how it must perform. Clarifying these points ensures the software engineering project can run on time and to budget and avoid disappointment.

**Q: Should I include diagrams in a functional requirements document?**
A: It is not 100% essential to include diagrams in a functional requirements document. However, diagrams can help improve understanding of how a system works so can be very useful for clarification purposes. Diagrams are especially useful for explaining processes.

**Q: How can I get help with defining functional and non functional requirements?**
A: Helping to bring ideas into reality through software engineering is what Enkonix does day-in-day-out. We help shape a concept through linking the strategy, design and engineering elements. This includes developing clear functional requirements. Why not contact us today to see how we can help with your functional and non functional requirements?