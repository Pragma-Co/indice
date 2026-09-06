# Commit Message Standard
 
## Objective
 
Standardize commit messages to make the project history easier to read, improve change traceability, and clearly identify the purpose of each modification.
 
## Convention Used
 
The project uses the **Conventional Commits** standard.
 
Commit messages must be written in English, be objective, and clearly represent the change being made.
 
## Standard Format
 
When there is a related task or card:
 
```text
<type>(#<id>): <short message in English>
```
 
Example:
 
```text
feat(#12): implement document search filters
```
 
When there is no related card, the requirement identifier can be used:
 
```text
<type>(<FR or NFR>): <short message in English>
```
 
Example:
 
```text
docs(FR1): document multidimensional search
```
 
**Priority:** when a change is associated with both a card/task and a requirement (FR/NFR), always use the card's `#id` in the commit message, ensuring traceability with the board/project management tool used by the team.
 
## Examples
 
```text
feat(#12): add document search filters
fix(#18): prevent unauthorized document access
test(#24): add document ingestion tests
docs(FR2): update document processing documentation
devops(#30): update CI/CD workflow
```
 
## Commit Types
 
| Type | Description |
|---|---|
| `feat` | New feature |
| `fix` | Bug fixes or incorrect behavior corrections |
| `docs` | Documentation changes |
| `style` | Formatting or style changes without behavior changes |
| `refactor` | Refactoring without functional changes |
| `test` | Adding or modifying tests |
| `chore` | Auxiliary tasks, dependencies, and configuration |
| `devops` | Changes related to CI/CD, automation, and infrastructure |
 
## Best Practices
 
- Use imperative verbs.
- Keep the first line short and objective.
- Describe what was changed, not just the modified file.
- Avoid generic messages such as `update`, `changes`, or `fix`.
- Keep each commit related to a logical and coherent change.
