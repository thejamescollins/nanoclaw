# GitHub Monitor Instructions

## Overview
Scheduled task that runs every 1 minute to monitor GitHub activity for tectalic-lex and automatically respond to new items requiring attention.

## Task Schedule
- **Frequency**: Every 1 minute (60000ms interval)
- **Context Mode**: Group (has access to conversation history and memory)

## Actions to Perform

### 1. Check GitHub for New Activity
Check for the following types of activity assigned to @tectalic-lex:
- Issues assigned to you
- Pull requests where your review is requested
- Mentions of @tectalic-lex in comments

### 2. Acknowledge New Items
- Add 👀 (eyes) emoji reaction to new comments/issues/PRs that require attention
- This indicates you've seen the item and are processing it

### 3. Action Items Based on Type

#### For Assigned Issues:
1. Create a thorough implementation plan that solves the issue
2. Post the plan as a comment on the issue
3. Wait for approval (thumbs up) from the user
4. When approved:
   - Implement the plan in a new feature branch, using descriptive atomic commits
   - Push the feature branch to GitHub
   - Create a pull request for review
   - Assign the PR to the user for review

#### For Pull Request Reviews:
1. Review the PR code changes
2. Add comments/feedback as appropriate
3. Provide a review status (approve/request changes/comment)

#### For Automated Bot Comments (e.g., `claude` bot):
When the `claude` bot or other automated tools comment on your pull requests:
1. **DO NOT trust the suggestions blindly** - automated feedback is often noisy or incorrect
2. **Critically review each suggestion** to determine if it's:
   - A real, valid concern that needs addressing
   - An important issue vs. a minor style preference
   - Actually relevant to the code changes
3. **Verify the concern** by examining the code yourself
4. **Only action items** that are genuinely valuable improvements
5. **Ignore or dismiss** suggestions that are:
   - Nitpicky or purely cosmetic
   - Based on misunderstanding of the code
   - Not worth the effort to address
6. **Use your judgment** - you're the engineer, not the bot

#### For Mentions:
1. Read the context of the mention
2. Respond appropriately based on what's being asked
3. Take any requested actions

#### For Chief PRD Implementation:
When asked to implement a Chief PRD (e.g., `chief customer-redact`):
1. **Read the PRD files**:
   - `.chief/prds/<prd-name>/prd.md` for human context
   - `.chief/prds/<prd-name>/prd.json` for machine-readable stories
2. **Consult `/workspace/group/chief-workflow-instructions.md`** for detailed workflow
3. **Work sequentially** through user stories by priority order (lowest number = highest priority)
4. **One story at a time**:
   - Read acceptance criteria carefully
   - Follow TDD when specified (write failing tests first, then implement)
   - Implement all acceptance criteria
   - Run tests and linters
   - Commit with format: `feat: [US-XXX] - Story Title`
   - Update `progress.md` with what was done, files modified, patterns discovered
5. **After all stories complete**:
   - Run full test suite
   - Verify all acceptance criteria met
   - Push branch and create PR

### 4. Reporting
- **DO NOT** send a message if there is no new activity
- Only notify when there's something requiring attention or action taken

## Important Notes
- Never add Co-Authored-By lines to commit messages
- Use atomic descriptive commits when implementing features
- Do NOT merge pull requests - always assign to user for review
- Follow the project's coding standards and patterns
- Check CLAUDE.md files for project-specific instructions
