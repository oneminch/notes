---
alias: Software Development Lifecycle
---

- SDLC (Software Development Life Cycle) is a systematic approach to software development that aims to minimize project risks and ensure that software meets customer expectations during delivery
- It consists of several key stages:
    - ==Planning==: includes tasks like cost-benefit analysis, scheduling, resource estimation, and allocation, with a software requirement specification document setting expectations and defining common goals for project planning.
    - ==Requirement Analysis==: involves gathering, analyzing, and documenting software requirements from the client, stakeholders, and end-users.
    - ==Design==: involves software engineers analyzing requirements and identifying the best solutions to create the software, considering factors like integration with existing IT infrastructure.
    - ==Build== / ==Implementation==: involves coding the product, with the development team analyzing requirements to identify smaller coding tasks that can be completed daily to achieve the final result.
    - ==Documentation==
    - ==Testing==: involves automation and manual testing to check the software for bugs and ensure it meets customer requirements.
    - ==Deployment==: involves releasing the software to production and making it available to end-users.
    - ==Maintenance==
- [[CI & CD|CI/CD]] helps automate and integrate these stages to enable faster and more reliable releases.
- SDLC models include the waterfall model, Agile model, iterative model, spiral model, and V-shaped model, each with its own unique process for addressing project challenges.
- Security is critical to the SDLC, with DevSecOps practices emphasizing the integration of security assessments throughout the entire SDLC to ensure that software is secure from initial design to final delivery.
- Effective communication across the entire team is the most important best practice to implement into an SDLC.
- Common mistakes and challenges in SDLC implementation include failing to account for and accommodate customer and stakeholder needs, project complexity, and a lack of strict adherence to all aspects of the parameters and design plans.

## SDLC Models

### Waterfall

- This model is a linear and sequential model that follows a strict order of phases:
    - Requirements -> Design -> Implementation -> Verification -> Maintenance
        - There's no going back to a certain stage.
        - Decisions from previous phase are permanent.
- It emphasizes strong documentation, low customer involvement, and a sequential structure with little room for changes once a phase is completed.

- **Advantages**:
    - Simple to understand and manage
    - Suitable for small or mid-sized projects with clear requirements
    - High management simplicity due to its rigidity
- **Disadvantages**:
    - Not suitable for complex or object-oriented projects
    - Feedback loop can be slow, making it difficult to apply changes
    - High risks and uncertainty due to lack of flexibility

### Agile

- This model is designed to facilitate change and eliminate waste processes.
- It emphasizes collaboration, working software, and customer involvement.
- It is highly adaptive and encourages iterative development, allowing for frequent changes based on customer feedback.

- **Advantages**:
    - Quick delivery of working projects
    - Emphasizes collaboration and direct communication
    - Allows for frequent changes based on customer feedback
- **Disadvantages**:
    - High dependency on customer interaction
    - Documentation is often completed at later stages
    - May not be suitable for projects with strict requirements or limited resources
- Before Agile:
    - Waterfall
- Agile frameworks
    - Scrum
    - XP
    - Lean
    - Kanban
- Other solutions:
    - BFC / Toyota Way
- Each Agile methodology tends to follow the same basic process, which includes:
    - Project planning
    - Product roadmap creation
    - Release planning

#### The Agile Manifesto

> **[The Agile Manifesto](https://agilemanifesto.org/)**

- **Values**:
    - ==Individuals and interactions== over processes and tools
    - ==Working software== over comprehensive documentation
    - ==Customer collaboration== over contract negotiation
    - ==Responding to change== over following a plan
- **Principles**
    - MVP through continuous delivery
    - Welcome changes in requirements
    - Collaboration across domains
    - Trust in motivated individuals
    - Direct communication
    - Sustainable development
    - Reflection and adaptation
- **Key Components**
    - User stories
        - High-level definitions of a work request.
        - A short, simple description is written from the user’s perspective and focuses on outlining what your client wants (their goals) and why.
        - It contains minimal information so the team can produce a reasonable estimate of the effort required to accomplish the request.
        - General format: "As a _user_ I want to _functionality_ so that _motivation._"
            - e.g. As a manager, I want to be able to understand my team members' progress, so I can better report our success and failures.
    - Sprints
        - A short iteration, usually between one to three weeks to complete, where teams work on tasks determined in the sprint planning meeting.
        - Continuously repeated until the product is feature ready.
        - Once the sprint is over, review the product to see what is and isn’t working, make adjustments, and begin another sprint to make improvements.
        - Implemented in the Agile framework Scrum but not in some other frameworks like Kanban.
    - Stand-up meetings
        - Daily stand-up meetings (under 10 minutes), also known as “daily Scrum meetings,” are a great way to ensure everyone is on track and informed.
        - Participants are required to stay standing to keep the meetings short and to the point.
    - Agile board
        - Helps teams track the progress of a project.
        - Can be a whiteboard with sticky notes, a simple Kanban board, or a function within a project management software.
    - Backlog
        - As project requests are added through the intake system, they become outstanding stories in the backlog.
        - During Agile planning, teams will estimate story points to each task.
        - During sprint planning, stories in the backlog are moved into the sprint to be completed during the iteration.
        - Managing the backlog is a vital role for project managers in an Agile environment.
- **Mindset**
    - Learning
    - Doing
    - Reflection
    - Adaptation

- Common components of Agile project management:
    - **Themes**
        - High-level objectives that guide the overall direction of product development.
        - Broad in scope and typically align with business goals or strategic initiatives.
        - e.g. Improve user engagement
    - **Epics**
        - Large bodies of work that can be broken down into smaller, more manageable pieces.
        - Usually too big to be completed in a single sprint and often span multiple sprints or even releases.
        - e.g. Redesign user profile page
    - **User Stories**
        - Short, simple descriptions of a feature from the end-user's perspective.
        - Typically follow the format: `As a <type of user>, I want <goal> so that <benefit>.`
        - Typically weighted using relative units of measurement called ==story points==.
        - e.g. for the "Redesign user profile page" epic:
            - "As a user, I want to upload a profile picture so that I can personalize my account."
            - "As a user, I want to add my interests to my profile so that I can connect with like-minded people."
            - "As a user, I want to see my recent activity on my profile so that I can track my engagement."
    - **Tasks**
        - The smallest units of work, typically representing specific activities required to implement a user story.
        - Usually technical in nature and assigned to individual team members.
        - e.g. for the "Upload a profile picture" user story:
            - "Create API endpoint for image upload"
            - "Implement front-end image upload component"
            - "Add image cropping functionality"
            - "Write unit tests for image upload feature"

#### Extreme Programming (XP)

- XP is an Agile software development framework that emphasizes frequent iteration, collaboration, and adaptation.
- It is based on five core values:
    - **Communication**
        - XP recognizes that problems often arise due to a lack of communication.
        - It emphasizes continuous and constant communication among team members, managers, and customers.
    - **Simplicity**
        - XP believes in "doing the simplest thing that could possibly work" and implementing a new capability in the simplest possible way.
    - **Feedback**
        - XP encourages frequent feedback, with working software delivered early to the customer for feedback and necessary changes.
    - **Courage**
        - XP provides courage to developers to focus on what is required, communicate and accept feedback, tell the truth about progress and estimates, refactor the code, adapt to changes, and throw away code when necessary.
    - **Respect**
        - Everyone contributes value, and management respects the right of developers to accept responsibility and receive authority over their own work.

#### Scrum

- Scrum is a framework for Agile development that is designed to facilitate rapid development and delivery of high-quality software.
- It is based on a set of principles and practices that promote continuous improvement, collaboration, and transparency:
    - It is based on the idea of _breaking down complex projects_ into smaller, more manageable pieces and iterating on them over time.
    - It promotes the idea of _iterative and incremental development_, with each iteration adding new features and functionality.
    - It emphasizes the importance of _self-organizing teams_, where team members are empowered to make decisions and take ownership of their work.
    - It promotes _transparency_ by encouraging regular communication and collaboration between team members and stakeholders.
- It divides the entire project into small incremental builds, with each iteration lasting from one to three weeks.
- It is often used in conjunction with other Agile methodologies, such as Kanban, to create a more comprehensive approach to software development.
- Scrum ceremonies
    - aka Scrum events, Scrum meetings, or Agile ceremonies
    - form the backbone of the methodology
        - Spring Planning
        - Daily Stand-up
        - Sprint Review
        - Sprint Retrospective
    - Sprint planning occurs before the sprint, daily stand-up meetings take place during the sprint, and the review and retrospective come after the sprint has ended.

##### Example: Building a Task Management App

- **Initial Product Vision:** A web-based application that allows users to create, assign, and track tasks within a team.

- **Sprint 0: Project Initiation (1 week)**
    - The Product Owner creates an initial product backlog with high-level features.
    - The team conducts a brief planning session to set up the development environment and agree on technical stack (e.g., React for frontend, Java for backend, Postgres for database).

- **Sprint 1: Basic Task Creation (2 weeks)**
    - **Sprint Planning:**
        - The team selects items from the product backlog to work on.
        - They break down the selected items into smaller tasks.
    - **Selected User Stories:**
        1. As a user, I can create a new task with a title and description.
        2. As a user, I can view a list of all tasks.
    - **Daily Standup Meetings:**
        - Team members briefly discuss progress and any blockers.
    - **Development:**
        - Frontend developers create basic UI for task creation and listing.
        - Backend developers set up API endpoints for creating and retrieving tasks.
    - **Testing:**
        - QA performs tests on the implemented features.
    - **Sprint Review:**
        - The team demonstrates the working features to stakeholders.
    - **Sprint Retrospective:**
        - The team discusses what went well and what could be improved.

- **Sprint 2: Task Assignment and Status (2 weeks)**
    - **Sprint Planning:**
        - The team selects new items from the updated product backlog.
    - **Selected User Stories:**
        1. As a user, I can assign a task to a team member.
        2. As a user, I can change the status of a task (e.g., To Do, In Progress, Done).
    - **Development and Testing:**
        - The team implements and tests these new features.
    - **Sprint Review and Retrospective:**
        - The team showcases the new functionality and reflects on the sprint.

- **Sprint 3: User Authentication (2 weeks)**
    - **Selected User Stories:**
        1. As a user, I can create an account and log in.
        2. As a user, I can only see tasks assigned to me or created by me.
    - **Development and Testing:**
        - Implement user authentication and authorization.
        - Modify existing features to respect user permissions.

- **Sprint 4: Task Filtering and Sorting (2 weeks)**
    - **Selected User Stories:**
        1. As a user, I can filter tasks by status.
        2. As a user, I can sort tasks by due date or priority.
    - **Development and Testing:**
        - Implement filtering and sorting functionality.

- **Sprint 5: Notifications (2 weeks)**
    - **Selected User Stories:**
        1. As a user, I receive email notifications when a task is assigned to me.
        2. As a user, I can set reminders for tasks.
    - **Development and Testing:**
        - Implement email notification system.
        - Create reminder functionality.

- **Final Sprint: Polishing and Deployment (2 weeks)**
    - Fix any remaining bugs.
    - Optimize performance.
    - Prepare for deployment.

- Throughout this process, the team maintains flexibility.
- If market conditions change or user feedback suggests different priorities, the Product Owner can adjust the product backlog, and the team can adapt in the next sprint.

---

## Further

### Videos 🎥

![What does larger scale software development look like?](https://www.youtube.com/watch?v=Dl-BdxNRUqs)
