---
description: Review and improve atomicity of all markdown files in the knowledge base
---

# Review Atomicity of Knowledge Base

You are tasked with reviewing the atomicity of all markdown files in this engineering standards knowledge base and creating a plan to improve them.

## Reference Document

First, read the atomic knowledge base principles:
- File: `principles/atomic-knowledge-base.md`

## Step 1: Inventory All Markdown Files

Find all markdown files in the topic directories:
- `naming/*.md`
- `testing/*.md`
- `architecture/*.md`
- `git-workflow/*.md`
- `principles/*.md`

**Exclude** from review:
- Root-level files: `README.md`, `CLAUDE.md`, `CHANGELOG.md`
- Template files in `templates/`
- Example files in `examples/`
- Index files (`index.md`) in each directory

## Step 2: Evaluate Each File

For each file, read it and assess against these atomic criteria:

### Atomicity Checklist
1. **Single Concept**: Does it focus on ONE specific idea/pattern/practice?
2. **Appropriate Length**: Is it 20-100 lines (roughly)? Flag if significantly longer or shorter.
3. **Code Examples**: Does it include practical code examples where applicable?
4. **Related Links**: Does it link to 2-5 related notes?
5. **Clear Title**: Is the filename descriptive of the single concept?

### Scoring
Rate each file as:
- ✅ **Atomic**: Meets all criteria
- ⚠️ **Needs Minor Improvements**: Mostly atomic, 1-2 issues
- ❌ **Not Atomic**: Multiple issues, needs significant work

## Step 3: Create Structured Report

Generate a report with:

### Summary Statistics
- Total files reviewed: X
- Atomic (✅): X
- Needs minor improvements (⚠️): X
- Not atomic (❌): X

### Detailed Findings

For each **non-atomic** file, provide:
- **File**: `path/to/file.md`
- **Status**: ⚠️ or ❌
- **Issues**:
  - [ ] Issue 1 description
  - [ ] Issue 2 description
- **Recommendation**: Brief suggestion (split, merge, add examples, add links, etc.)

## Step 4: Create Improvement Plan

Based on findings, create a categorized plan:

### Files to Split (Too Broad)
List files covering multiple concepts that should be split into separate atomic notes.

For each:
- Current file: `path/to/file.md`
- Proposed new files:
  - `path/to/concept-1.md` - Brief description
  - `path/to/concept-2.md` - Brief description
- Migration: How to handle links from other files

### Files to Enhance (Missing Elements)
List files that are atomic but missing examples or links.

For each:
- File: `path/to/file.md`
- Add: Code example / Related links / Other enhancement

### Files to Merge (Too Small/Related)
List files that are too granular and should be combined.

For each:
- Files to merge: `file1.md`, `file2.md`
- Into: `new-combined-file.md`
- Rationale: Why these belong together

## Step 5: Get User Approval

Present the complete plan and ask:

> I've analyzed all markdown files and created an improvement plan. The plan includes:
> - X files to split
> - X files to enhance
> - X files to merge
>
> Would you like me to proceed with these changes? You can also ask me to:
> - Show details for specific files
> - Skip certain changes
> - Modify the approach for any file

Wait for user confirmation before proceeding.

## Step 6: Execute Approved Changes

Once approved, systematically:

1. **For splits**: Create new files, update links, archive old file
2. **For enhancements**: Add missing examples/links
3. **For merges**: Combine content, update all references, remove old files
4. **Update index files**: Reflect any new/removed/renamed files

After each change, mark it complete and move to the next.

## Important Notes

- Be thorough but concise in your analysis
- Use the atomic-knowledge-base.md principles as the source of truth
- When in doubt, prefer splitting over merging (atomicity is key)
- Always update cross-references when moving/splitting content
- Preserve all existing examples and useful content
