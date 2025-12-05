# Testing Pattern Matching

Pattern matching syntax for running tests on specific stories or components.

## Why Use Patterns?

**Benefits**:
- Test specific components during development
- 10x faster than running full suite
- Focus on what you're actively working on

**Performance**:
- Full suite: ~60 seconds (100 stories)
- Single component: ~5 seconds (5 stories)

## Basic Syntax

Patterns are passed as positional arguments after `--`:

```bash
npm run storybook:test -- <pattern>
```

The pattern is passed to Jest as a regex for matching test file paths.

## Basic Pattern Examples

### Test by Component Name

```bash
npm run storybook:test -- Button
```

Tests all files matching "Button":
- `Button.stories.js`
- `ButtonGroup.stories.js`
- `SubmitButton.stories.js`

### Test Specific File

```bash
npm run storybook:test -- FileDropzone.stories
```

Tests only `FileDropzone.stories.js` (all stories in that file).

### Test by Directory

```bash
npm run storybook:test -- components/forms
```

Tests all story files in `components/forms/` directory.

### Test Multiple Components

```bash
npm run storybook:test -- 'Button|Dialog|Modal'
```

Uses regex `|` (OR) to test multiple components.

## Escaping Special Characters

### Square Brackets (Next.js Dynamic Routes)

Square brackets in paths must be escaped because they are regex character classes.

**Without escaping** (fails):
```bash
# [t_id] interpreted as character class matching t, _, i, or d
npm run storybook:test -- 'src/app/[t_id]/[p_id]/devices'
# Result: 0 matches
```

**With escaping** (works):
```bash
npm run storybook:test -- 'src/app/\[t_id\]/\[p_id\]/devices'
# Result: matches all device stories
```

**Full path example**:
```bash
npm run storybook:test -- 'src/app/\[t_id\]/\[p_id\]/devices/_components/SideFilter'
```

### Other Special Characters

Characters with regex meaning need escaping:

| Character | Meaning | Escape |
|-----------|---------|--------|
| `[` `]` | Character class | `\[` `\]` |
| `.` | Any character | `\.` |
| `*` | Zero or more | `\*` |
| `+` | One or more | `\+` |
| `?` | Optional | `\?` |
| `(` `)` | Group | `\(` `\)` |
| `^` | Start anchor | `\^` |
| `$` | End anchor | `\$` |

## Regex Patterns

Full regex syntax supported:

### Starts With

```bash
# All components starting with "Export"
npm run storybook:test -- '^Export'
```

Matches: `ExportDialog`, `ExportButton`, etc.

### Ends With

```bash
# All Dialog components
npm run storybook:test -- 'Dialog$'
```

Matches: `ConfirmDialog`, `AlertDialog`, etc.

### Multiple Exact Matches

```bash
npm run storybook:test -- '(Button|Card|Table)'
```

Matches only: `Button`, `Card`, `Table`

### Path Matching

```bash
# Only stories in ui directory
npm run storybook:test -- 'ui/'

# Only stories in components/forms
npm run storybook:test -- 'components/forms/'
```

## Filtering Strategies

### By Feature Area

```bash
# Authentication components
npm run storybook:test -- 'src/components/auth'

# Navigation components
npm run storybook:test -- 'src/components/nav'

# Form components
npm run storybook:test -- 'src/components/forms'
```

### By Complexity

```bash
# Simple presentational components
npm run storybook:test -- 'src/components/ui'

# Complex container components
npm run storybook:test -- 'src/components/containers'
```

### Recent Changes

```bash
# Find recently modified story files
git diff --name-only main | grep stories

# Test those specific files
npm run storybook:test -- 'FileDropzone|ExportDialog'
```

**Automated**:
```bash
# Get changed story files and test them
changed_files=$(git diff --name-only main | grep stories | sed 's/.stories.js//' | xargs basename -a | paste -sd "|" -)
npm run storybook:test -- "$changed_files"
```

## Case Sensitivity

Patterns are **case-insensitive** by default (Jest adds `/i` flag):

```bash
# Both match Button.stories.js
npm run storybook:test -- button
npm run storybook:test -- Button
```

**Tip**: Use exact component name case for clarity.

## Common Patterns

### One Component

```bash
npm run storybook:test -- ComponentName
```

### All Forms

```bash
npm run storybook:test -- 'forms/'
```

### All Dialogs and Modals

```bash
npm run storybook:test -- 'Dialog|Modal'
```

### All UI Components

```bash
npm run storybook:test -- 'ui/'
```

### Next.js Dynamic Route Components

```bash
# Specific component in dynamic route
npm run storybook:test -- 'src/app/\[t_id\]/\[p_id\]/devices/_components/DeviceCard'

# All components in a dynamic route folder
npm run storybook:test -- '\[t_id\]/\[p_id\]/devices'
```

## Limitations

### Cannot Test Single Story

Test-runner operates at the **file level** only.

To test a single story within a file:

**Option 1**: Use Storybook dev UI
```bash
npm run storybook:dev
# Navigate to specific story in browser
```

**Option 2**: Temporarily rename other files
```bash
mv Button.stories.js Button.ignore.stories.js
npm run storybook:test
mv Button.ignore.stories.js Button.stories.js
```

**Option 3**: Comment out other stories (not recommended)

### No Metadata Filtering

Not supported:
- Testing by tag/label
- Testing by category
- Testing by story metadata

**Workaround**: Use consistent naming conventions.

## Combining with Other Options

### Pattern + Watch Mode

```bash
npm run storybook:test -- Button --watch
```

Re-runs tests when Button.stories.js changes.

### Pattern + Verbose

```bash
npm run storybook:test -- Dialog --verbose
```

Shows detailed output for Dialog stories only.

## Troubleshooting

### Pattern Returns 0 Matches

1. **Check for unescaped special characters**:
   ```bash
   # Wrong
   npm run storybook:test -- 'src/app/[t_id]'

   # Right
   npm run storybook:test -- 'src/app/\[t_id\]'
   ```

2. **Check path exists**:
   ```bash
   find src -name "*.stories.*" | grep -i "yourpattern"
   ```

3. **Try simpler pattern first**:
   ```bash
   # Start broad, then narrow down
   npm run storybook:test -- ComponentName
   npm run storybook:test -- 'folder/ComponentName'
   npm run storybook:test -- 'full/path/ComponentName'
   ```

### Pattern Matches Too Many Files

Use more specific path:
```bash
# Too broad - matches all SideFilter components
npm run storybook:test -- SideFilter

# Specific - matches only devices SideFilter
npm run storybook:test -- 'devices/_components/SideFilter'
```

## Related Notes

- [Testing Development Workflow](/storybook/testing/testing-development-workflow.md) - Daily testing and debugging patterns
- [Test Runner Filtering](/storybook/testing/test-runner-filtering.md) - Ignoring stories with `.ignore.` pattern
