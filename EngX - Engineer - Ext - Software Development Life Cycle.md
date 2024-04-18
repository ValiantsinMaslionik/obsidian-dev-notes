#engx/engineers 

# Traditional Methods

## Waterfall model

### Overview

Requirements -> Analysis and Design (High-level design/Specifications) -> Development -> Tests -> Development and Maintenance (deployed to PROD)

Was origin from production manufactory - production plant.
All planning is done upfront with detailed planning documentation. and the scope of work is generally fixed.

**Waterfall/manufacturing approach**
- includes well-defined check-list, processes and tools.
- If error is introduced at one step, it will be propagated to all further steps.
	Incorrect requirement => Incorrect design => Incorrect implementation
- fixed scope
- comprehensive system documentation

**What's wrong**
- later delivery of business value
- frozen requirements
- precedence of detailed checklist/control processes over people

### Application

**Impact of Waterfall**

- Project failures
- Comprehensive documentation
- Detailed checklist

**Applicable Scenarios**
- Simple systems where the team knows the system and requirements very well
- Enhancement in ongoing maintenance
- Mission critical system

## Spiral model

Mix of Waterfall and Iterative approaches.

Looks like a series of waterfalls.

Each waterfall consists of:
Iteration 1: Planning (Requirement identification and analysis) -> Risk Analysis (Risk-related activities: identification, prioritization, mitigation)-> Engineering (Software implementation/Tests) -> Evaluation (Review and feedback from stakeholders, next iteration planning)... Iteration X:...

Each iteration is different from the previous one: the next iteration is more complex then the previous.
Risk management is integrated part of Spiral Model.

1-st waterfall: Prototype
2-nd waterfall: Release candidate
3-rd waterfall: Launch

Key takeaways:
- Iterative nature of the software development process
- Risk driven - prototyping is actively used

## Rational Unified Process (RUP)

- Software development process framework.
- Large knowledge base.
- Artifact, processes, templates, phases, and disciplines.
- Customizable by plug-ins
- Was influenced by with UML (Unified Modelling Language).

Inception (Lifecycle Objectives Milestone: all stakeholders are agree what are you going to build) -> Elaboration (Lifecycle Architecture Milestone: base lining the architecture of a software) -> Construction (Initial Operational Capability Milestone: software is ready to be used by end users) -> Transition (Production Release Milestone: fine tune an app)

- Phases are container for a small waterfall-like iterations.
- Each phase ends with a milestone

RUP Phases

![[zIMG-SDLC-Rup-Disciplines.png]]

RUP Shortcoming

- Customizable, but heavy
- Prescriptive

**Best practices:**

- Develop iteratively
- Manage requirements, change
- Continuously verify quality
- Model visually, component-based architecture

