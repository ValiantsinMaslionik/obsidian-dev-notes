#ai/llm 

Once you've performed the task research and thoroughly understand what you want to accomplish and how to do it, the next phase is task decomposition. It consists of three major parts:

#### Break the task down

Divide a task into smaller, more manageable subtasks.
Tasks and subtasks are activities you must perform to attain the required functionality.
Each subtask should have its inherent value and be independently useful. This means you can collect user feedback on the functionality built into a subtask and use it to improve the outcome of completing the subtask. To do this, consider the components and functional aspects specified during the task research phase.

#### Create a hierarchy

Creating a hierarchy during task decomposition allows tasks and subtasks to be organized logically and precisely. This makes prioritizing work, assigning resources, and measuring progress easier. Such a systematic approach fosters improved cooperation, risk management, and project efficiency.

#### Specify dependencies

Understanding task dependencies is critical for good planning because it ensures activities are executed appropriately and helps avoid bottlenecks and delays.

You can complete this task in separate sequential prompts or combine them into one prompt. Continuing the same conversation with LLM is also important if you have already explored the task. This will ensure that LLM is fully aware of the context and can provide the most accurate response. If you happen to have a pre-built set of described tasks, make sure to include it in the prompt.

Take a look at an example of a task decomposition prompt.

### Example

#### Task

After investigating and understanding your task of adding the new feature "Recovering a Forgotten Password," you must divide it into small, manageable chunks. Also, you need to understand the hierarchy of these subtasks and their dependencies.

#### Prompt
Thank you. Before starting, I'd like to ensure a comprehensive task breakdown. Consider the functional aspects and components you provided me above. Decompose the task effectively and create a hierarchy with dependencies.

#### Output

Here's a breakdown of the 'Forgotten Password' feature development, structured as a task hierarchy with dependencies, suitable for a project management tool:

**Epic: Implement "Forgotten Password" Feature**

1. Backend Development (Spring Boot)
	1.1 Design and Create Database Schema
		1.1.1 Add password_reset_token table (token, user_id, expiry_date)
	1.2 Implement Password Reset Service
		1.2.1 Create PasswordResetService class.
		1.2.2 Implement token generation (cryptographically secure).
		1.2.3 Implement token persistence using PasswordResetTokenRepository.
		1.2.4 Implement token validation (expiry, user association).
		1.2.5 Implement password update logic (hashing).
	Dependency: 1.1
	1.3 Create Password Reset Controller
		1.3.1 Create PasswordResetController class.
		1.3.2 Define endpoints for /forgot-password, /reset-password.
		1.3.3 Handle requests, delegate logic to PasswordResetService.
	Dependency: 1.2
	1.4 Implement Email Service
		1.4.1 Choose and configure email provider/library (Spring Mail, etc.).
		1.4.2 Create EmailService to send emails with reset links.
	Dependency: 1.2
	1.5 Secure Endpoints
		1.5.1 Configure SecurityConfig to protect sensitive endpoints.
		1.5.2 Implement rate limiting (if necessary).
	Dependency: 1.3

2. Frontend Development (Angular)
	2.1 Create Forgot Password Component
		2.1.1 Create forgot-password component (HTML, TS, CSS).
		2.1.2 Implement form for email input and submission.
	Dependency: 1.3
	2.2 Create Reset Password Component
		2.2.1 Create reset-password component (HTML, TS, CSS).
		2.2.2 Implement form for new password (with validation and confirmation).
	Dependency: 3.1
		2.3 Implement Authentication Service
		2.3.1 Create AuthService to handle API communication.
		2.3.2 Implement forgotPassword method (calls backend endpoint).
		2.3.3 Implement resetPassword method (calls backend endpoint).
	Dependency: 1.3
3. Integration and Testing
	3.1 Integrate Frontend and Backend
		3.1.1 Configure communication between Angular and Spring Boot.
	Dependency: 1.3, 2.3
	3.2 End-to-End Testing
		3.2.1 Write tests to cover the entire password reset flow.
	Dependency: 1.5, 2.1, 2.2
4. Deployment
	4.1 Deploy to Staging Environment
	Dependency: 3.2
	4.2 User Acceptance Testing (UAT)
	Dependency: 4.1
	4.3 Production Deployment
	Dependency: 4.2

Notes:
This is a high-level breakdown. Adjust the granularity and dependencies based on your project needs. Task assignments, durations, and resources should be added to your project management tool. Don't forget about documentation and communication throughout the development process.

> Follow up with the questions below (or similar ones) to get more information from LLM about performing your task:
> - What key subtasks or components should be included in the task decomposition?
> - Are there any specific functionalities that can be treated as separate subtasks?
> - Guide me on how to organize the subtasks into a hierarchical structure.
> - Are there any particular subtasks that are more foundational and should be tackled first?
> - How should I determine the order in which subtasks should be completed based on their dependencies?
> - Are there any subtasks that can be done in parallel or worked on at the same time?

### Next step

Follow up with the questions below (or similar ones) to get more information from LLM about performing your task:

- What key subtasks or components should be included in the task decomposition?
- Are there any specific functionalities that can be treated as separate subtasks?
- Guide me on how to organize the subtasks into a hierarchical structure.
- Are there any particular subtasks that are more foundational and should be tackled first?
- How should I determine the order in which subtasks should be completed based on their dependencies?
- Are there any subtasks that can be done in parallel or worked on at the same time?

It may also be good to request that LLMs produce a diagram based on the generated tasks. A visual representation can be quite useful in understanding the linkages and dependencies between various tasks. It provides a comprehensive picture of the project flow, making monitoring progress and detecting any bottlenecks easy. You can request diagrams in various formats, but the most popular are PlantUML and Mermaid.

#### Task

Visualize the "Forgotten Password" feature implementation using a PlantUML hierarchical diagram.

#### Prompt

Based on the previously discussed task hierarchy for implementing the "Forgotten Password" feature, generate PlantUML code for a hierarchical diagram. Represent tasks as rectangles with rounded corners, use dashed arrows for dependencies, and group frontend and backend tasks separately.

#### Output

```uml
@startuml

skinparam rectangle {
  roundCorner 10
}

left to right direction

rectangle "Frontend (Angular)" {
    rectangle "ForgotPasswordComponent" as FPC {
      rectangle "Display email form"
      rectangle "Send email (AuthService)"
    }

    rectangle "ResetPasswordComponent" as RPC {
      rectangle "Retrieve token from URL"
      rectangle "Display new password form"
      rectangle "Send token & password (AuthService)"
    }

    rectangle "AuthService" as AS {
      rectangle "forgotPassword(email)"
      rectangle "resetPassword(token, newPassword)"
    }

    FPC ..> AS
    RPC ..> AS
}

rectangle "Backend (Spring Boot)" {
  rectangle "PasswordResetController" as PRC {
    rectangle "requestPasswordReset(email)"
    rectangle "resetPassword(token, newPassword)"
  }

  rectangle "PasswordResetService" as PRS {
    rectangle "generateResetToken(user)"
    rectangle "validateResetToken(token)"
    rectangle "resetPassword(user, newPassword)"
  }

  rectangle "EmailService" as ES {
    rectangle "sendPasswordResetEmail(user, token)"
  }
  
  PRC ..> PRS
  PRC ..> ES
}

rectangle "Database" as DB {
  rectangle "PasswordResetToken Table"
  rectangle "User Table" 
}

AS ..[#red]> PRC : Sends HTTP requests to
PRS ..> DB
ES ..> DB

@enduml
```

**Explanation:**

- Rectangles with Rounded Corners: The skinparam rectangle line ensures that all rectangles have rounded corners.
- Dashed Arrows: The ..> and ..[#red]> notation creates dashed arrows for dependencies.
- Grouping: Frontend and backend tasks are grouped separately using larger rectangles with labels "Frontend (Angular)" and "Backend (Spring Boot)".
- Hierarchy: Sub-tasks are nested within their parent components or services. For instance, "Display email form" is a sub-task of ForgotPasswordComponent.
- Database Interaction: Dependencies on the database are shown with arrows from PasswordResetService and EmailService to the "Database" rectangle.
- HTTP Communication (Red Arrow): The communication between the frontend's AuthService and the backend's PasswordResetController is highlighted with a red, dashed arrow and a label.

This PlantUML code generates a clear and informative hierarchical diagram representing the 'Forgotten Password' feature implementation, including dependencies and component relationships.

#### Visualization

![[Pasted image 20250414234354.png]]