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

**Plan-Do-Check-Act” (PDCA) method**

The <span style="color:lime;">“Plan-Do-Check-Act” (PDCA)</span> approach starts with planning a new practice that your team considers necessary at a certain point or checking a hypothesis you have. Then you implement your plan, study how it works in a real environment, and analyze whether the implemented feature brings the expected results. Finally, you act based on the lessons learned in the previous step. If something went wrong, your team should start another PDCA cycle with a better plan. But if the initial plan was successful, you can incorporate your findings into the system and plan new improvements. To make PDCA effective, everyone on your team should contribute to it. *Retrospective meetings* are the right time to conduct this practice regularly.

- **Plan**
Establish objectives and processes required to deliver the desired results.

- **Do**
Carry out the objectives from the previous step.

- **Check**
During the check phase, the data and results gathered from the do phase are evaluated. Data is compared to the expected outcomes to see any similarities and differences. The testing process is also evaluated to see if there were any changes from the original test created during the planning phase. If the data is placed in a chart it can make it easier to see any trends if the plan–do–check–act cycle is conducted multiple times. This helps to see what changes work better than others and if said changes can be improved as well.

- **Act/Adjust**
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

In short, employing CI/CD practices helps your project become more Agile.

# Taking an Agile Approach to a Software Development Project

When it comes to being responsive to business changes, an Agile approach to a software development project is much more flexible and effective than the traditional waterfall approach. **The phases in a waterfall SDLC happen in a linear sequence.** In contrast, **an Agile SDLC might have several implementation versions**. For example, the Scrum-based Agile approach has short iterations and allows several phases to happen simultaneously.

While it is impossible to describe a universal SDLC that reflects the specifics of every project, it is still important to imagine how a team might go through SDLC phases in terms of a timeline and iterations in every phase of a project end-to-end. The example below is based on the CI/CD approach within an Agile-driven environment. It illustrates when and how a team can implement Agile engineering practices, processes, and tools to ensure successful delivery.

![](https://elearn.epam.com/assets/courseware/v1/afd6087bbf046069a8205d080a647181/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_in_Agile-Driven_Software_Development_scrum_framework_scheme.svg)

This sample project is conducted using the Scrum Framework on a two-week iteration basis. It supports the Agile principles of receiving and being able to implement valuable feedback faster. The stages the team follows are not linear—for example, when team members finish the Requirements, Design, and Planning stages and begin the Development and Testing stages for the current iteration, they also start working on the Requirements, Design, and Planning for the next iteration. Engaging in software development activities **in parallel** helps make your work as efficient as possible.

Applying Engineering Excellence practices, processes, and tools during the Development and Testing phases in this baseline project is essential for successful delivery.

### The Development Phase

An Agile software development team usually conducts development and testing activities simultaneously. Pieces of code can be tested as they are completed, which provides the early feedback required to improve other development outcomes. The Development phase covers **pre-coding activities, coding, and code review**. Throughout this phase, a software development team is also involved in other development activities: knowledge sharing and management of technical debt.

 **Pre-Coding Activities**

![](https://elearn.epam.com/assets/courseware/v1/8fd45145de80e39cd56fce955e3a70e0/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_in_Agile-Driven_Software_Development_pre-coding_activities.svg)

Before your team starts coding, you must check all existing tasks against the **Definition of Ready** (DoR) criteria to ensure that the requirements are clear and your team will be able to measure each task’s completion. This preparatory work helps ensure that the team is on the right track and will be developing the requested functionality instead of wasting time and effort to produce an increment with unexpected behavior. As a result of this step, a team is ready to start the implementation of tasks.

 **Coding**

![](https://elearn.epam.com/assets/courseware/v1/cdbb30279b69cb9a654d1cb0606e5413/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_in_Agile-Driven_Software_Development_coding.svg)

Usually, software developers divide larger coding tasks into smaller ones that they can complete in one or two days. To ensure production-ready code, developers should:

- **Follow the same coding standards and coding principles.** This helps prevent common technical defects and improves code readability and maintainability. As a result, product quality improves, and tasks take less time to implement.
- **Support all development activities with automated static code analysis tools.** These tools provide immediate feedback if a developer creates a technical defect or breaks coding standards.
- **Check every code change with comprehensive automated testing.** Automated testing is mandatory for every development task. It shortens verification time and reduces the number of possible code defects. As a result, developers can safely change the code. One type of automated testing—unit testing—is prioritized since it provides the fastest feedback.

**Code Review**

![](https://elearn.epam.com/assets/courseware/v1/56e49309b14c2532d097da51ab631633/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_in_Agile-Driven_Software_Development_code_review.svg)

Code review is mandatory to perform before integrating a code change to a common source code. By reviewing code for possible defects and coding standards violations that the automated verification does not cover, developers ensure high code quality.

 **Other Development Activities**

![](https://elearn.epam.com/assets/courseware/v1/99d10219e32dd1949ea0c9c7a0ef73a5/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_in_Agile-Driven_Software_Development_other_dev_activities.svg)

Throughout the Development phase, team members should actively participate in knowledge sharing activities and managing their technical debt.

- **Knowledge sharing:** While coding, a team pays a lot of attention to sharing project-related knowledge among the team members to make sure everyone is on the same page. Usually, this occurs during dedicated knowledge sharing sessions. Code review is also an example of a knowledge sharing activity, as it involves several developers providing feedback to each other’s code.
- **Technical debt management:** Sometimes, developers might need to make a shortcut and implement code in a non-optimal way. This creates technical debt, which works only as a temporary solution. A team must track these shortcuts and pay off the debt by fixing them later.

To consider a coding task done, a team follows the **definition of done (DoD)** for development activities, which stands for a set of rules, checks, and quality gates that a task should pass. After a task passes the DoD, the development phase is over, and testers can start verifying the piece of code.

### The Testing Phase

The Testing phase covers pre-testing activities, automated and manual test execution, and defect management.

![](https://elearn.epam.com/assets/courseware/v1/e963502c953d240aa3c393142a1f5e2a/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_in_Agile-Driven_Software_Development_pre-testing_activities.svg)

**Pre-Testing Activities**

Pre-testing activities start at the same time developers begin the implementation of their tasks. Using special test case management tools, software testing engineers create test cases for new functionality covering all possible business scenarios. Their goal is to help identify as many defects as possible and speed up feedback when testing.
Once the test cases are ready, the testing engineers start to implement “to-be-automated” test cases. Software developers can support them in these activities.

**Automated and Manual Test Execution**

Once code is ready for testing, testers deploy it to a Quality Assurance (QA) environment. Immediately after that, they execute automated tests to ensure the functionality is not broken and is suitable for further manual QA checks.
If the results of the automated tests are positive, manual QA engineers perform two types of functional testing: using previously written test cases and exploratory testing.
After that, QA engineers conduct non-functional testing that verifies specific aspects of the system, including performance, security, and usability.

**Defect Management**

QA engineers can identify defects by analyzing the results of both automated and manual testing. They submit the defects into the defect management system so that the developers can fix them, ideally during the same iteration. It is also important that QA engineers cover those defects with automated tests to prevent their re-appearance.
Once developers fix the defects, QA engineers verify the results.

When product increments successfully pass QA engineers’ verification, the code is ready for the next phase—UAT/Staging—where business users check it in a staging environment and/or in production.
Agile projects combine business and development teams that work together to enable a successful product delivery.

### Example of a Project Ecosystem

A **project ecosystem** is a technological environment that provides necessary tools and infrastructure that help a team perform their tasks efficiently. The example project ecosystem below represents how a team can properly implement CI/CD processes to enhance the agility of their software development process.
A typical cross-functional team working on an Agile-driven software development project would include a product owner on the customer side, and a project manager, developers, manual and automated testers, and system engineers on the development side.

![flat image](https://elearn.epam.com/assets/courseware/v1/0614f9ce5df1666fd7689f1a0d47124a/asset-v1:EPAM+EngX_B+0921+type@asset+block/Engineering_Practices_big_schema_new.svg)

Close cooperation and transparent communication among all team members are essential to reach agility goals. Agile principles can help your team create an SDLC that fully matches your client’s goals and gives them a competitive business advantage.

# Evolving Engineering Excellence

Now that you’ve discovered more about various engineering practices in Agile-driven software development, you are ready to deep dive into the different SDLC phases in the following lessons.
Remember that staying agile will help you effectively organize your tasks throughout the project and deliver value to your client irrespective of changing business environments.

![](https://elearn.epam.com/assets/courseware/v1/9592938cb0a21d420bb4301761f9f4c1/asset-v1:EPAM+EngX_B+0921+type@asset+block/Asset_38.svg)

### Best Practices for Ensuring Agility of Your Project

To ensure agility on your projects, your team must follow these best practices:

- Focus on the client’s goals and requirements
- Adhere to Engineering Excellence standards
- Implement key principles of CI/CD
- Follow key principles of Agile-driven software development:
    - Prioritize code quality first
    - Shift feedback left
    - Automate all repeatable tasks
    - Follow Lean and PDCA practices

https://www.linkedin.com/learning/software-development-life-cycle-sdlc/processes-for-software-projects?resume=false&u=2113185