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
When asked to run Chief PRDs (e.g., `chief customer-redact` or process all PRD branches):

**Single PRD:**
1. **Announce start**: Send message with PRD name and branch before starting
2. **Run the chief command**: `chief <prd-name>` in the wine-club directory (run in background)
3. **Monitor progress periodically**:
   - Every 120 seconds, run `chief list` to check progress
   - If progress percentage changes, send update message (e.g., "customer-redact: 3/8 stories (37%)")
   - Continue monitoring until 100% complete
4. **Handle issues if Chief gets stuck**:
   - Review error messages in the TUI
   - Check what story it's working on
   - Help debug or fix blocking issues
   - Resume with `s` after fixes
5. **After successful completion**:
   - Verify all user stories marked as complete
   - Check that tests pass
   - Review the commits made
   - Send completion message with results (tests passed, PR created, etc.)

**Queue Processing (Multiple PRDs):**
1. **Discover PRD branches**:
   - List all local branches
   - For each branch, checkout and run `chief list`
   - Parse output to identify unstarted PRDs (0/X, 0%)
   - Send message listing discovered PRDs to process
2. **Process each unstarted PRD**:
   - Send message before starting each PRD (branch name, PRD name)
   - Run `chief <prd-name>` in background
   - Monitor with `chief list` every 120 seconds
   - Send progress updates when percentage changes
   - Send completion message (tests passed, PR created, etc.)
   - After completion, automatically move to next branch
3. **Skip completed or partial PRDs**:
   - If `chief list` shows 100% complete, skip (already done)
   - If partial progress, skip (needs manual intervention)
4. **Report final summary** when all PRDs processed (total processed, any failures)

### 4. Reporting
- **DO NOT** send a message if there is no new activity
- Only notify when there's something requiring attention or action taken

## Important Notes
- Never add Co-Authored-By lines to commit messages
- Use atomic descriptive commits when implementing features
- Do NOT merge pull requests - always assign to user for review
- Follow the project's coding standards and patterns
- Check CLAUDE.md files for project-specific instructions
