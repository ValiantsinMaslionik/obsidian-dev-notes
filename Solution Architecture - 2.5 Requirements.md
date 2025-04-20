#solutionarchitecture #sa 

## System Capability

We must understand business capabilities (возможности) to be able to define/design system capability, which covers not only user and user journeys, but system processes and even the components of the system. Conway's Law can be a helpful tool in such cases.

![[Pasted image 20250420193444.png]]

#### Capability Map

> A capability map is a one way to define the relationship between business capabilities and system capabilities. It shows the reusability of system capabilities.

A business capability map (or business capability model) is a holistic set of capabilities that constitute what an enterprise does. It is not a process map, and it is not a value stream map. Building a business capability model is a non-trivial task. However, it is not necessary to spend countless hours and several quarters defining a business capability map.
- Building a business capability map involves creating the structure, hierarchy, and actual capabilities representing what the business does. A typical capability map starts from the high-level value chain and then is decomposed to lower levels of granularity.
- Buy a sample business capability model for a function or industry. Developing business capability models from the ground up can take too much time. A sample business capability model can accelerate time to value, particularly in light of the backlash in some companies that business architecture takes too long and delivers limited value.

![[Pasted image 20250420172041.png]]

Value of a capability map mostly lies in the analysis of the current vs. desired levels of capability, and in uncovering capabilities that the organization already possesses but does not recognize or manage explicitly. Capabilities and capability levels in a target business architecture give high-level direction for change. This is the core of capability-based planning.

Capabilities can be classified in a different way, e.g.
- strategic vs. operational vs. supporting
- core vs. non-core
- customer-facing vs. internal
- innovating vs. differentiating vs. commodity

## Classification

This is one of the most popular classifications outlined by the international institute of Business Analysis (2015) in its *A guide to the Business Analysis Body of Knowledge (BABOK)*.

![[Pasted image 20250420194121.png]]

- **Functional Requirements** - describe the capabilities that a solution must have in terms of (с точки зрения) the behavior and information that the solution will manage.
- Non-Functional Requirements - do not relate directly to the behavior of functionality of the solution, but rather describe conditions under which a solution must remain effective or qualities that a solution must have.

## Requirements example

**Example**

![[Pasted image 20250420194627.png]]

#### Stakeholder requirements

Describes the stakeholder needs that must be met in order to archive business requirements. They may serve as a bridge between business and solution requirements.

**Examples**

- Provide online booking capabilities through website and mobile application
- Automatically suggest relevant purchases to a customer

![[Pasted image 20250420195144.png]]

#### Transition requirements

Address topics such as data conversion, training, and business continuity. These are temporary by nature and describe the needs for a transition between two states.

- Data conversion and migration
- Having a tool doing the one round data conversion but then it could be wasted

#### Solution requirements

Describe the capabilities and qualities of a solution that meets stakeholder requirements. They provide the appropriate level of detail to allow for the development and implementation of the solution.


## RAID

#### Risk

A risk is any specific event which might occur and have a negative impact on your project or program. Each risk will have an associated probability of occurrence along with the impact on your project if it does materialize. An example of a risk might be that a change in tax law could mean that you will have to redo some of your project, and this will impact the schedule by *x* and cost *y*. As a project manage, it is your responsibility to ensure a risk management process is undertaken, managing and mitigating risks, along with ensuring risks are routinely and effectively communicated to your stakeholders.

#### Assumptions

An assumption is something we aet as true to enable us to proceed with our project or program. Typically, this happens during the planning and estimation phase of the project. As an example of an assumption, during the early planning phase we might assume that we have access to 10 skilled specialists for the entire duration of the project. By making this assumption, it enables us to produce our plan. Of this assumption turns out to be false, then the project is negatively impacted. Because assumptions can tun out to be false and impact your project adversely, it is your responsibility as project manager to monitor and manage all assumptions with minimal impact to the project.

#### Issues

An issue is a risk that has become a reality. An example: a team member might leave a project - this is a risk. However, if they do leave leave the project, then this is an issue that needs to be addressed.

#### Dependencies

A dependency exists when an output from one piece of works pr project is a mandatory input for another project or piece of work. An example of a dependency in a building project might be that the architectural diagrams need to be completed before the foundations can be laid. Managing inter-dependencies is critical to ensuring projects, regardless of their size, run smoothly. As project and program managers, it is your responsibility to record, monitor, and manage these dependencies.

## Conclusion

Business capability should be aligned with system capabilities where the people in business capability are mapped to users of the system capability, the business processes in business capability are mapped to system processes of the system capability.
