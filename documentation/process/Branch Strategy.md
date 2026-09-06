# Branch Strategy
 
## Objective
 
Organize the development of features, fixes, and project releases, allowing each team member to work independently while ensuring that changes are validated before reaching the application's main version.
 
## Convention Used
 
The project uses **Gitflow** as its branch management strategy.
 
This workflow separates feature development from the stable version of the system by using specific branches for each purpose.
 
## Branch Structure
 
```text
main
  │
  └── develop
        │
        ├── feature/*
        ├── fix/*
        ├── release/*
        └── hotfix/*
```
 
- **`main`**: contains the stable versions of the project.
- **`develop`**: integration branch for features developed during the development cycle.
- **`feature/*`**: used for developing new features.
- **`fix/*`**: used for fixes identified during development (non-urgent issues that follow the normal workflow through `develop`).
- **`release/*`**: used when necessary to prepare a new version for production (final adjustments and acceptance testing) before merging into `main`.
- **`hotfix/*`**: used when necessary to fix critical issues identified in production, allowing a quick fix directly from `main`, followed by a merge back into both `main` and `develop`.
## Standard Format
 
Working branches must follow the format:
 
```text
<type>/<id>-short-description-in-english
```
 
### Examples
 
For a new feature:
 
```text
feature/12-document-search
```
 
For a fix:
 
```text
fix/18-document-access-validation
```
 
For a documentation change:
 
```text
docs/21-update-installation-guide
```
 
## Development Workflow
 
The development of a feature generally follows this workflow:
 
```text
develop
   ↓
feature/*
   ↓
Development
   ↓
Testing
   ↓
Pull Request
   ↓
Code Review
   ↓
CI/CD
   ↓
develop
   ↓
Validation
   ↓
main
```
 
Changes must not be made directly to the `main` branch.
 
CI/CD is used as an automated verification mechanism before integration, helping identify build failures, test failures, or other checks configured in the pipeline.
 
