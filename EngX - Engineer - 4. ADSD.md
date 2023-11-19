#engx/engineers 

---

<span style="color:lime;">Software development life cycle (SDLC)</span> - the phases a project undergoes end-to-end - to manage resources, control costs, anticipate possible risks, and track the progress of each process required to develop a high-quality product.
There are many approaches to the SDLC, but two of the most common are **Waterfall** and a **Scrum-based Agile SDLC**. 
Every SDLC includes certain baseline elements, but because every project is different, the SDLC will also vary to reflect specific client needs and priorities.

# Basic SDLC

A typical SDLC is comprised of six phases.

#### Requirements

he objective of the first phase is to gather the customer’s initial requirements and expectations for the solution that the team will be creating.
The Requirements phase also includes defining the resources required to build the software.

#### Design

Once your team has a clear understanding of your customer’s technical and functional requirements, the Design phase includes more detailed work on the project, including a feasibility assessment. 
A team designs exactly how the system will look and function: its elements, components, security level, modules, architecture, various interfaces, and data types.

#### Software Development

The Software Development phase results in producing a system or a prototype that other team members can test. This phase covers the actual coding process, which takes a lot of the team’s efforts.
If the system includes hardware, its configuration and tuning for specific requirements and functions also happen in this phase.

#### Testing

The Testing phase ensures that the developed system or prototype meets the requirements gathered at the beginning of the project.
During this phase, a team records, tracks, corrects, and retests any defects until the product meets quality standards and all functions work as expected.
Testing activities can be manual or automated, and they may be performed in a specific simulation environment.

#### User Acceptance Testing (UAT)/Staging

During the UAT, or Staging, phase, selected business users test the solution and deployment procedures in an environment that resembles the production environment as much as possible. 
When UAT is complete and key stakeholders have signed off on the functionality, the software is released to production.

#### Maintenance. 

After the solution is released to end users, there still could be bugs the software development team did not identify during the previous phases. If bugs occur, the team must fix the issues and repeat the Testing and UAT/Staging phases.
The Maintenance phase also encompasses planning additional features for future releases. ==These tasks can lead to starting a new SDLC.==

# Building an Agile SDLC

An Agile approach throughout a project enables your clients to react quickly to end users’ requests and provide a high-quality product that satisfies customer needs and expectations.

The Agile Manifesto highlights how engineering practices help to enhance agility in the long term.
> Continuous attention to technical excellence and good design enhances agility. (9th principle of the Agile Manifesto)

While it is a common misconception that engineering practices are more important than the project outcome, focusing on client's goals is the way to go. 
Your client’s requirements should be your team’s top priority, but you must also ensure that the product meets the standards of engineering excellence to make the project successful in the long term.

Non-optimal technical solutions might be deemed acceptable as a temporary way to meet important, short-term project goals, but these solutions are not ideal and won’t work in the long term.
Making your SDLC Agile will empower the team to incorporate regular client feedback flexibly, prioritize the most valuable features, and produce a testable outcome. To reach these goals, your team should take two actions.

#### Make changes faster without increasing costs or degrading quality

Your client’s primary goal is to provide a high-quality product that will increase user satisfaction, bring value as soon as possible, and help the company gain a competitive market advantage. Clients also want to keep the software development process cost-effective.
Your team can help your clients reach those goals by prioritizing the introduction of changes to address any current solution gaps and improve product quality effectively. Regularly making changes saves time and money in the long run, while postponing changes can have a critical impact on your project - like having to rebuild your solution at a later stage.

#### Produce a potentially shippable (ready for detailed customer feedback) product increment **at the end of each iteration**.

Regular feedback from your client is crucial to ensure that you are on the same page about solution requirements. Your client might add or modify requirements at some point, and your team must be able to adjust quickly. Your team can prepare for quick adjustments by delivering a product increment with each iteration (e.g., a new feature) that is ready for client testing and a potential release.
This action is useful not only for trying out a real product with end users but also if your team needs to create a <span style="color:lime;">Minimum Viable Product (MVP)</span> to test a business idea. Engineers can use the feedback they receive on this bare minimum version for further improvement of the product.

# Key Principles of Agile-Driven Software Development

#### Prioritize Code Quality First

Following best code design principles ensures the team will have fewer issues to address at a later stage. As a result, a client can enjoy a high-quality solution faster and cheaper.

#### Shift Feedback Left

Shift all feedback you receive - from your customer, team members, and automated verification processes (e.g., unit tests, static code analysis) - to the left: the earliest point possible in the SDLC.
Seeking feedback earlier will make it cheaper for your team to fix any issues and resulting negative outcomes and enable your team to produce higher-quality code.

#### Automate All Repeatable Tasks

Automation saves time that developers can use for other tasks, which increases their productivity. Engineers can automate many repeatable tasks in the SDLC to receive reliable and fast feedback on the existing functionality. For example, your team can automate code quality checks, test execution, report generation, metrics collection, and more.

#### Follow Lean and PDCA Practices

Your team should continuously strive to assess your current software development processes and identify ways to improve them further. Lean and PDCA are two practices that directly support development speed and agility.

The term “lean” was coined by a research team headed by Jim Womack in the late 1980s to describe Toyota’s business practices. **Lean** stands for a set of practices and principles that can help your team maximize the value you bring to your customer while minimizing waste (non-optimal processes that add no value to the customer’s product or service). Examples of waste are useless meetings, tasks, and documentation, and ineffective working methods, such as multitasking. Lean encourages Agile engineers to work in iterations and get feedback on their work faster, enabling them to test business ideas with the help of MVPs. Lean applies to all SDLC phases.

# Plan-Do-Check-Act” (PDCA) method

The <span style="color:lime;">“Plan-Do-Check-Act” (PDCA)</span> approach starts with planning a new practice that your team considers necessary at a certain point or checking a hypothesis you have. Then you implement your plan, study how it works in a real environment, and analyze whether the implemented feature brings the expected results. Finally, you act based on the lessons learned in the previous step. If something went wrong, your team should start another PDCA cycle with a better plan. But if the initial plan was successful, you can incorporate your findings into the system and plan new improvements. To make PDCA effective, everyone on your team should contribute to it. *Retrospective meetings* are the right time to conduct this practice regularly.

#### Plan

Establish objectives and processes required to deliver the desired results.

#### Do

Carry out the objectives from the previous step.

#### Check

During the check phase, the data and results gathered from the do phase are evaluated. Data is compared to the expected outcomes to see any similarities and differences. The testing process is also evaluated to see if there were any changes from the original test created during the planning phase. If the data is placed in a chart it can make it easier to see any trends if the plan–do–check–act cycle is conducted multiple times. This helps to see what changes work better than others and if said changes can be improved as well.

#### Act/Adjust

Also called "adjust", this act phase is where a process is improved. Records from the "do" and "check" phases help identify issues with the process. These issues may include problems, non-conformities, opportunities for improvement, inefficiencies, and other issues that result in outcomes that are evidently less-than-optimal. Root causes of such issues are investigated, found, and eliminated by modifying the process. Risk is re-evaluated. At the end of the actions in this phase, the process has better instructions, standards, or goals. Planning for the next cycle can proceed with a better baseline. Work in the next do phase should not create a recurrence of the identified issues; if it does, then the action was not effective.

# Implementing the Key Principles with CI/CD

<span style="color:lime;">Continuous integration (CI)</span> goes hand in hand with <span style="color:lime;">continuous delivery (CD)</span>. Together, they represent an approach software development teams use to support an Agile SDLC.

M. Fowler defines **continuous integration** as a “software development practice where members of a team integrate their work frequently.”
While working on different parts of the solution, it is essential for your team to frequently validate that those parts work well together and make timely adjustments if needed.

In **continuous delivery** , teams produce software in short cycles that they can reliably release at any time. This approach aims at building, testing, and releasing software with greater speed and frequency. It helps reduce the cost, time, and risk of delivering changes by allowing for more incremental updates to applications in production. A straightforward and repeatable deployment process (that may or may not take the form of an automatic continuous deployment to production) is important for continuous delivery.

CI/CD embodies a practical implementation of key Agile SDLC principles. For example:

- CI helps your team implement the principle of code quality - during CI, engineers merge all code pieces and check the code quality on each integration regularly and often.
- Both CI and CD run tests as early as possible, which means shifting engineering feedback left. 
	- CI provides early and regular feedback from development tools
	- During CD, a team receives feedback from QA tools.
- CI/CD encourages automation and helps a team automate all necessary and repeatable tasks.
