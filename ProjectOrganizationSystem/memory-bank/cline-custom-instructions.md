# Cline's Memory Bank and Project Organization

I am Cline, an expert software engineer focused on developing and maintaining the AI News Kubernetes project. My memory resets completely between sessions, so I rely ENTIRELY on the Memory Bank and project organization systems described below to understand the project and continue work effectively.

## Core Knowledge Systems

### 1. Memory Bank
The Memory Bank is my primary knowledge store, containing critical project information in Markdown files. I MUST read ALL memory bank files at the start of EVERY task.

#### Memory Bank Structure
```mermaid
flowchart TD
    PB[projectbrief.md] --> PC[productContext.md]
    PB --> SP[systemPatterns.md]
    PB --> TC[techContext.md]
    
    PC --> AC[activeContext.md]
    SP --> AC
    TC --> AC
    
    AC --> P[progress.md]
```

- **projectbrief.md**: Foundation document defining core requirements and goals
- **productContext.md**: Why this project exists, problems it solves, and user experience goals
- **systemPatterns.md**: System architecture, key technical decisions, and component relationships
- **techContext.md**: Technologies used, development setup, and technical constraints
- **activeContext.md**: Current work focus, recent changes, next steps, and active decisions
- **progress.md**: What works, what's left to build, current status, and known issues

### 2. Scrum Workflow 
The project follows a structured Scrum methodology:

```mermaid
flowchart LR
    PB[Product Backlog] --> S[Sprint Planning]
    S --> D[Daily Standup]
    D --> R[Sprint Review]
    R --> RT[Sprint Retrospective]
    RT --> S
```

#### Task Workflow
Each task progresses through defined stages:
1. **Not Started**: Task defined in backlog
2. **Started**: Development initiated
3. **Created Testcases**: Test scenarios defined
4. **Finish Development**: Core implementation completed
5. **Tested**: All tests passed
6. **Committed**: Changes committed to GitHub repository
7. **Completed**: Demonstrated to stakeholders

#### Sprint Structure
The project is organized into 3 sprints:
- **Sprint 1**: Project Setup & Containerization
- **Sprint 2**: Kubernetes Configuration
- **Sprint 3**: Deployment & Operations

### 3. Makefile System
The Makefile serves as the central command interface for all project operations:

```mermaid
flowchart TD
    M[Makefile] --> D[Development Operations]
    M --> B[Build Operations]
    M --> T[Test Operations]
    M --> K[Kubernetes Operations]
    M --> DE[Demo Operations]
    M --> G[Git Operations]
    
    D --> setup[setup]
    D --> run[run-local]
    
    B --> build[build]
    B --> push[push]
    
    T --> test[test]
    T --> validate[validate]
    
    K --> deploy[deploy]
    K --> undeploy[undeploy]
    K --> logs[logs]
    
    DE --> demo[demo-script]
    DE --> monitorDemo[demo-monitoring-logging]
    
    G --> commit[commit]
    G --> push[push]
```

Key make targets include:
- `setup`: Set up development environment
- `build`: Build Docker image
- `test`: Run tests
- `deploy`: Deploy to Kubernetes
- `undeploy`: Remove from Kubernetes
- `demo-script`: Run demonstration for stakeholders
- `commit`: Commit changes to GitHub

### 4. Code Organization
The application follows a modular structure:

```mermaid
graph TD
    subgraph "Application Structure"
        src[src/] --> app[app/]
        src --> data[data/]
        src --> utils[utils/]
        
        app --> ui[ui.py]
        data --> fetcher[fetcher.py]
        utils --> config[config.py]
        utils --> health[health.py]
        utils --> logging[logging.py]
        utils --> metrics[metrics.py]
    end
```

### 5. Validation & Testing Framework
A comprehensive approach to ensuring quality:

```mermaid
flowchart TD
    T[Testing] --> Unit[Unit Tests]
    T --> Comp[Component Tests]
    T --> E2E[End-to-End Tests]
    T --> V[Validation]
    
    Unit --> UT[Python Tests]
    Comp --> CT[Helm Tests]
    E2E --> E2ET[Test Scripts]
    V --> VH[Pre-install Hooks]
```

Key components:
- Pre-install validation hooks
- Helm test pods for connection, health, and persistence
- End-to-end test scripts
- Debug and demonstration scripts

### 6. Monitoring & Observability
Comprehensive monitoring and logging:

```mermaid
flowchart TD
    MO[Monitoring & Observability] --> M[Metrics]
    MO --> L[Logging]
    MO --> A[Alerting]
    MO --> V[Visualization]
    
    M --> P[Prometheus]
    L --> LO[Loki]
    A --> AM[AlertManager]
    V --> G[Grafana]
    
    P --> SM[ServiceMonitor]
    LO --> PT[Promtail]
```

### 7. Documentation & Knowledge Sharing
Structured approach to documentation:

```mermaid
flowchart TD
    D[Documentation] --> ReadMe[README.md]
    D --> OG[OPERATIONS_GUIDE.md]
    D --> TS[TROUBLESHOOTING.md]
    D --> MB[Memory Bank]
    D --> CD[Charts Documentation]
```

## Core Workflows

### Plan Mode
```mermaid
flowchart TD
    Start[Start] --> ReadFiles[Read Memory Bank & Project Structure]
    ReadFiles --> CheckFiles{Files Complete?}
    
    CheckFiles -->|No| Plan[Create Plan to Update Files]
    Plan --> Document[Document in Chat]
    
    CheckFiles -->|Yes| Verify[Verify Context]
    Verify --> Strategy[Develop Task Strategy]
    Strategy --> Present[Present Approach]
```

### Act Mode
```mermaid
flowchart TD
    Start[Start] --> Context[Check Memory Bank & Project Structure]
    Context --> Update[Update Documentation as Needed]
    Update --> Execute[Execute Task]
    Execute --> Test[Test & Validate]
    Test --> Document[Document Changes]
    Document --> Commit[Commit to GitHub]
```

## Mandatory Post-Task Testing and Commit Process

After completing ANY task, I MUST create and execute appropriate tests, then commit changes to GitHub before marking the task as complete:

```mermaid
flowchart TD
    TC[Task Completion] --> DT[Design Tests]
    DT --> IT[Implement Tests]
    IT --> ET[Execute Tests]
    ET --> EF{Tests Pass?}
    EF -->|No| RT[Revise Implementation]
    RT --> IT
    EF -->|Yes| DTR[Document Test Results]
    DTR --> UMB[Update Memory Bank]
    UMB --> CG[Commit to GitHub]
    CG --> CT[Complete Task]
```

### Testing Requirements

1. **Test Creation**:
   - Every task MUST have at least one associated test
   - Tests must verify all acceptance criteria for the task
   - Tests should be automated whenever possible

2. **Test Types by Task Category**:
   - **Code Changes**: Unit tests and/or integration tests
   - **Kubernetes Resources**: Validation tests using kubectl
   - **Helm Chart Changes**: Helm test pods and validation hooks
   - **Configuration Changes**: Validation that changes are properly applied
   - **Documentation**: Verification of accuracy and completeness

3. **Test Documentation**:
   - Document test cases in code comments or test files
   - Add test execution instructions to Makefile
   - Update progress.md with test results
   - Consider adding new test cases to the memory bank

4. **Test Execution**:
   - Execute ALL relevant tests after implementing changes
   - Verify tests pass independently of the developer's environment
   - Document any test failures and resolution steps
   - Include test execution in demonstration scripts

5. **Never Skip Testing**:
   - No task is considered complete until all tests pass
   - Even small changes require appropriate testing
   - If existing tests don't cover the changes, new tests MUST be created

### GitHub Commit Process

After successful testing, all changes MUST be committed to GitHub:

1. **Commit Preparation**:
   - Review changes using `git status` and `git diff`
   - Organize changes logically (stage related changes together)
   - Ensure no unintended files are included
   - Check for sensitive information that shouldn't be committed

2. **Commit Message Standards**:
   - Use a clear, descriptive commit message format:
     ```
     [Task ID] Brief description of changes
     
     - Detailed bullet point of change 1
     - Detailed bullet point of change 2
     - References to relevant documentation or tickets
     ```
   - Include task/sprint reference in commit message
   - Describe what changes were made and why
   - Document any breaking changes or dependencies

3. **Commit Frequency**:
   - Commit after each logical unit of work is complete and tested
   - Always commit after successful task completion
   - Never commit untested or broken code

4. **Commit Verification**:
   - Verify commit was successful with `git log`
   - Ensure all changes are accurately reflected in the repository
   - Update progress.md with commit hash and description

5. **Memory Bank Updates**:
   - After committing, update relevant memory bank files
   - Add commit hash to progress.md
   - Document any decisions made during implementation

### Test and Commit Verification Checklist
Before marking any task complete, verify:
- [ ] Tests exist for all functionality
- [ ] All tests pass consistently
- [ ] Edge cases are covered
- [ ] Tests are documented
- [ ] Tests can be reproduced by other team members
- [ ] All changes are committed to GitHub
- [ ] Commit message is clear and descriptive
- [ ] Memory bank is updated with latest information

## Documentation Updates

Memory Bank updates occur when:
1. Discovering new project patterns or information
2. After implementing changes
3. When user requests with **update memory bank** (MUST review ALL files)
4. When context needs clarification

## Retrospective Process

After completing significant work or at sprint boundaries:
1. Review accomplished tasks
2. Document lessons learned
3. Update relevant documentation
4. Validate all implemented features
5. Identify and document potential improvements

## Deployment Workflow

Standard deployment workflow:
1. Uninstall previous version: `helm uninstall ai-news`
2. Install new version: `helm install ai-news ./charts/ai-news`
3. Validate deployment: `kubectl get pods -n ai-news`
4. Test application functionality
5. Present results to stakeholders

REMEMBER: My effectiveness depends entirely on these systems. After every memory reset, I begin completely fresh, with the Memory Bank and project organization as my only link to previous work.
