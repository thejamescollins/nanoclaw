# Chief Workflow Instructions

## Overview

Chief is an autonomous coding agent that processes Product Requirement Documents (PRDs) through a "Ralph Wiggum loop" - each iteration starts fresh, but nothing is forgotten. This solves the context window problem by creating new Claude sessions for each user story while maintaining project history.

## How Chief Works

### The Ralph Loop

1. **Story Selection**: Identifies the next incomplete, highest-priority user story
2. **Claude Invocation**: Constructs a prompt with story details and project context
3. **Code Generation**: Reads files, writes code, runs tests, resolves issues
4. **Commit**: Creates a commit with format: `feat: [US-XXX] - Story Title`
5. **State Update**: Marks story complete in progress.md
6. **Iteration**: Repeats for remaining stories

### State Management

The `progress.md` file maintains "institutional memory" across fresh sessions:
- Implemented features
- Modified files
- Discovered patterns and gotchas
- Lessons learned

This preserves context without token overhead.

## PRD Structure

PRDs live in `.chief/prds/<prd-name>/` with two files:

### prd.md (Human-Readable)
- Introduction and goals
- User stories with acceptance criteria
- Functional requirements
- Technical considerations
- Success metrics

### prd.json (Machine-Readable)
```json
{
  "project": "Feature Name",
  "description": "...",
  "userStories": [
    {
      "id": "US-001",
      "title": "Story title",
      "description": "As a [role], I need [capability] so [benefit]",
      "acceptanceCriteria": ["criterion 1", "criterion 2"],
      "priority": 1,
      "passes": false
    }
  ]
}
```

## Implementation Workflow

### When Asked to Implement a Chief PRD

Example: "chief customer-redact"

1. **Read the PRD files**:
   - `.chief/prds/<prd-name>/prd.md` for full context
   - `.chief/prds/<prd-name>/prd.json` for machine-readable stories

2. **Work sequentially by priority**:
   - Process stories in priority order (lowest number = highest priority)
   - Complete each story fully before moving to the next

3. **For each user story**:

   a. **Read acceptance criteria carefully**

   b. **Follow TDD when specified**:
      - Write failing tests first (red phase)
      - Implement to make tests pass (green phase)
      - Verify all tests pass

   c. **Implement the story**:
      - Make all changes required by acceptance criteria
      - Follow project patterns and conventions
      - Run linters and tests

   d. **Commit with standard format**:
      ```
      feat: [US-XXX] - Story Title

      - Acceptance criterion 1
      - Acceptance criterion 2
      - Acceptance criterion 3
      ```

   e. **Update progress.md**:
      - Mark story as complete
      - Document files modified
      - Note any patterns or gotchas discovered
      - Record lessons learned

4. **Final verification**:
   - Run full test suite
   - Run linters
   - Verify all acceptance criteria met
   - Update documentation as specified

## Key Principles

### One Story, One Commit
Each user story produces exactly one complete feature with its own isolated commit. This makes debugging straightforward.

### Idempotency
Implementations should be safe to run multiple times without side effects or errors.

### Institutional Memory
Always update progress.md with:
- What was implemented
- What files were modified
- Patterns discovered
- Problems encountered and solutions
- Gotchas for future stories

### Follow Existing Patterns
- Check similar existing code before implementing
- Match the project's coding style and conventions
- Use established patterns (e.g., ShopRedactJobTest.php patterns)
- Respect the project's architecture

## Example Story Processing

```markdown
### US-001: Add redacted_at column to customers table
**Priority:** 1
**Description:** As a developer, I need a redacted_at timestamp...

**Acceptance Criteria:**
- [ ] Migration adds nullable redacted_at timestamp column
- [ ] Migration runs successfully (php artisan migrate)
- [ ] IDE helpers regenerated after model change
```

**Implementation steps:**
1. Create migration file
2. Add nullable timestamp column
3. Run migration to verify
4. Regenerate IDE helpers
5. Commit: `feat: [US-001] - Add redacted_at column to customers table`
6. Update progress.md

## Important Notes

- Never add Co-Authored-By lines to commit messages
- Follow project's git commit conventions
- Run tests after each story
- Don't skip acceptance criteria
- Document gotchas for future stories
- Each commit should be atomic and complete
