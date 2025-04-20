#ai/llm 

## Best Practices for LLM Usage

1. Clearly Define the Problem

Start by clearly stating the task or problem, including the core issue and its impact on the project or users.

2. Specify the Tech Stack

Mention the technologies, frameworks, or programming languages you are using to ensure the AI provides relevant advice.

3. Ask Targeted Follow-Up Questions

After the initial prompt, ask more specific questions to dig deeper into details like best practices, edge cases, and potential challenges.

4. Iterate and Refine

Continue the conversation to get increasingly specific and actionable information, ensuring all aspects of the task are thoroughly explored.

5. Provide Examples

Include specific examples of inputs and expected outputs to provide context.

6. Ensure Code Quality

Focus on maintainability, readability, adherence to clean code and clean design principles and coding standards when using AI-generated code as a starting point.

> Remember that it is your responsibility to evaluate the applicability of the suggestions and verify that the integrated solution does not introduce new issues.

In software development, task research is comparable to daily planning. It involves breaking down coding tasks into smaller, manageable parts, analyzing their requirements, and creating a clear execution roadmap. This is like organizing your day, deciding what to do first, and setting time for each task. By conducting thorough task research, developers can operate more efficiently, organize their work better, achieve better results, and experience a smoother growth process.

## Task Research

To perform a task effectively, the task or problem statement must be understood completely. While researching the task of creating a new feature, keep the following points in mind:

#### Functional aspects

Essential task requirements that cannot be overlooked to develop new features properly. This includes specific operations, data processing, user interactions, and any other action the software must perform to meet user needs and achieve its objectives accurately and effectively.

#### Non-Functional aspects

Components of the task relate to quality attributes such as performance, scalability, security, usability, and maintenance. These requirements ensure that the new feature not only works properly but also corresponds with the overall system goals and user expectations, resulting in a robust and user-friendly final product.

#### Components

The fundamental code structure necessary to complete a task. This includes identifying the classes, methods, functions, and modules that will make up the new feature. Properly designed components ensure that the software is modular, reusable, and easy to debug and extend.

Here is a generalized formula you can start with to create an effective prompt for task research. Consider including the highlighted key points when designing a prompt for LLM to perform this task:

![[Pasted image 20250414230140.png]]

**Activity**
Define the action you want to take in relation to the development task. Examples include understanding requirements, breaking down tasks, organizing subtasks, identifying dependencies, and exploring solutions.

Task and Problem
This description provides context so LLM can offer relevant, valuable information or advice. Consider incorporating the following aspects into your description:
- A task that you are trying to complete
- A problem or question you are dealing with. 
Detailing these points allows LLM to provide targeted advice and solutions that address your specific needs.
- Technologies or keywords linked to the task or problem.
Providing this information ensures that the responses you receive are technically appropriate and directly relevant to the tools you're using.

### Example

#### Task
Consider your task to add a new feature, "Recovery of the Forgotten Password," to an application since users have encountered the problem of being unable to reset their passwords when they forget them. To begin your research, define the functional aspects.

#### Prompt
*In this example, details about the project are unnecessary and may be omitted as the feature of recovering passwords is quite common. However, when implementing more unique features, mentioning details about your project may be crucial if you need tailored or specific outputs.*
I am about to start coding a new feature for our healthcare platform, which facilitates online patient visits: "Recovering a Forgotten Password." Currently, users can't reset their passwords if they forget them. Given that we use Spring Boot for the backend and Angular for the frontend, what should be the key functionalities for this feature? Also, what major code components should I focus on to ensure the password recovery process is smooth and secure?

#### Output
Great question! Implementing a 'Forgotten Password' feature is crucial for any online platform, especially one dealing with sensitive healthcare data. Here's a breakdown of the key functionalities and code components to focus on:

**Key Functionalities:**

**Request Password Reset:**
- Input Field: A clear input field for the user to enter their registered email address.
- Validation: Client-side validation (using Angular) to check if the entered email is in a valid format.
- Backend Check: Verify if the provided email is associated with an existing account (using your Spring Boot backend).
- Rate Limiting: Implement rate limiting (in the backend) to prevent brute-force attempts by limiting password reset requests from a single IP address within a specific time frame.
**Generate and Send Reset Token:**
- Secure Token Generation: Upon successful email verification, generate a unique, cryptographically secure, and time-limited token (consider using Java's java.security.SecureRandom class).
- Persistent Storage: Store this token securely. Avoid storing it in plain text in your database. Consider hashing it using a strong hashing algorithm like bcrypt.
- Email Delivery: Send an email to the user containing a link with the embedded reset token. This email should have clear instructions and the link should have a limited lifespan (e.g., expires in 24 hours).
**Password Reset Page:**
- Token Validation: When the user clicks the link, validate the token on the backend:
	- Check if the token exists in your database.
	- Verify if it's not expired.
- New Password Input: Provide input fields for the user to enter their new password and confirm it.
- Password Requirements: Enforce strong password requirements (e.g., minimum length, a mix of uppercase, lowercase, numbers, and special characters) using both client-side and server-side validation.
- Password Hashing: Hash the new password using a robust hashing algorithm like bcrypt before storing it in the database. Never store passwords in plain text.

**Major Code Components (Illustrative):**

**Frontend (Angular):**
- Forgotten Password Component: Handles user input, validation, and communication with the backend for sending the reset request.
- Password Reset Component: Handles token validation, new password input, validation, and communication with the backend to update the password.
**Backend (Spring Boot):**
- Controller: Exposes endpoints for receiving reset requests, validating tokens, and updating passwords.
- Service: Contains the core logic for generating tokens, sending emails, validating input, and interacting with the repository.
- Repository: Handles database operations related to finding users, storing and verifying tokens, and updating passwords.
- Security Configuration: Configures Spring Security to handle authentication and authorization, especially for protecting the password reset endpoints.
- Email Service: Handles sending emails with reset links using a reliable email provider.
**Additional Considerations:**
- Security: Prioritize security at every step. Use HTTPS to encrypt communication, protect against common vulnerabilities like cross-site scripting (XSS) and SQL injection, and follow secure coding practices.
- Usability: Make the process intuitive and user-friendly. Provide clear instructions, error messages, and a visually appealing interface.
- Accessibility: Ensure the feature is accessible to users with disabilities, following accessibility guidelines.

By focusing on these functionalities and components, you'll be well on your way to building a secure and user-friendly 'Forgotten Password' feature for your healthcare platform!

#### Prompt 2

*Continue with the following step to specify the fundamental code structure necessary to implement this task.*

Thank you. Now, can you describe the code structure needed to pull this off? Specifically, I need to know the best way to handle the user data securely on the backend and how to set up the frontend with Angular for a smooth user experience. What backend services and frontend components should I be looking at?

#### Output 2

You're welcome! Here's a more detailed look at the code structure, focusing on secure data handling and a smooth user experience:

**Backend (Spring Boot):**
```txt
src
├── main
│   └── java
│       └── com
│           └── yourcompany
│               └── healthcareplatform
│                   └── passwordreset
│                       ├── service
│                       │   ├── PasswordResetService.java
│                       │   └── EmailService.java
│                       ├── controller
│                       │   └── PasswordResetController.java
│                       ├── model
│                       │   └── PasswordResetToken.java
│                       └── repository
│                           └── UserRepository.java 
│                   └── user
│                       └── User.java
│                   └── security
│                       └── SecurityConfig.java
└── ... other project files and folders
```

- User.java (Entity): * Represents a user in your system. * Fields: id, email, passwordHash (never store passwords in plain text!), enabled (for account activation/deactivation), etc.
- PasswordResetToken.java (Entity): * Stores password reset tokens. * Fields: id, token (hashed using bcrypt or a similar algorithm), user (reference to the User entity), expiryDate.
- UserRepository.java (Repository): * Uses Spring Data JPA to interact with the database for user-related operations. * Methods: findByEmail(String email), etc.
- PasswordResetService.java (Service): * Core logic for password reset. * Methods: * requestPasswordReset(String email): Generates token, saves to the database, sends email. * validatePasswordResetToken(String token): Checks if the token is valid and not expired. * resetPassword(String token, String newPassword): Updates the user's password with the new hashed password.
- EmailService.java (Service): * Handles sending emails using a provider like JavaMailSender or a third-party library. * Method: sendPasswordResetEmail(String to, String resetLink).
- PasswordResetController.java (Controller): * Handles HTTP requests related to password reset. * Endpoints: * POST /api/password-reset/request: Receives email, initiates reset process. * GET /api/password-reset/reset?token={token}: Validates the token, renders the reset password form. * POST /api/password-reset/reset: Receives the new password, updates the user entity.
- SecurityConfig.java (Security Configuration): * Configures Spring Security to allow access to the /api/password-reset/** endpoints without authentication. * Protects other sensitive endpoints with appropriate authentication mechanisms.

**Frontend (Angular):**

```txt
src
└── app
    └── password-reset
        ├── password-reset-request
        │   └── password-reset-request.component.ts
        └── password-reset
            └── password-reset.component.ts
    └── app-routing.module.ts
    └── ... other frontend components
```

- password-reset-request.component.ts: * Contains the form for the user to enter their email address. * Uses Angular's HttpClient to send a POST request to /api/password-reset/request. * Provides feedback to the user (success message, error messages).
- password-reset.component.ts: * Retrieves the token from the URL parameters. * Makes a request to /api/password-reset/reset?token={token} to validate the token (could be done in a route guard). * Displays a form for the user to enter their new password (twice for confirmation). * Uses HttpClient to send a POST request to /api/password-reset/reset with the new password. * Provides feedback to the user.
- app-routing.module.ts: * Defines routes for the password reset components.

**Security and User Experience:**

- HTTPS: Use HTTPS to encrypt all communication between the frontend and backend.
- Input Validation: Validate email format on both the frontend and backend. Enforce strong password requirements.
- Error Handling: Provide clear and informative error messages to the user.
- Rate Limiting: Implement rate limiting on the backend to prevent brute-force attacks.
- Token Expiration: Ensure tokens have a short lifespan and are invalidated after use.
- Accessibility: Follow accessibility guidelines for forms, input fields, and error messages.

This structure helps separate concerns, ensures secure data handling, and provides a good foundation for building a robust and user-friendly 'Forgotten Password' feature!

> Do not disconnect from LLM after the first output. Following this prompt, ask further specific questions regarding your task to deepen your understanding and refine your approach. You may use the following or similar questions:
> - What are the best practices for implementing a secure password recovery feature?
> - Are there any common security risks with password recovery features that I should be aware of?
> - Do I need to handle any special inputs or outputs, such as two-factor authentication or security questions?
> - Could you suggest any tools or libraries that could simplify the implementation of this feature?