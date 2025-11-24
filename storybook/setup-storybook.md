# Storybook Setup Guide

Complete configuration checklist for setting up Storybook in a Next.js project following our engineering standards.

## Overview

This is the entry point for configuring Storybook correctly. Follow this checklist to ensure all configuration files are in place and properly configured.

**Time to complete**: ~30 minutes for fresh setup, ~15 minutes to audit existing setup

---

## Prerequisites

### Required Packages

```bash
npm install --save-dev \
  @storybook/nextjs \
  @storybook/test-runner \
  @storybook/addon-a11y \
  @storybook/addon-docs \
  @chromatic-com/storybook \
  storybook \
  playwright \
  concurrently \
  wait-on
```

**Version requirements**:
- Storybook: `^9.0.0`
- Next.js: `^15.0.0`
- Playwright: `^1.48.0`

---

## Configuration Files Checklist

### ✅ 1. Main Configuration (`.storybook/main.js`)

**Purpose**: Core Storybook configuration

**Required settings**:
```javascript
const path = require('path');

const config = {
  framework: "@storybook/nextjs",

  stories: [
    "../src/**/*.mdx",
    "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)",
  ],

  addons: [
    "@chromatic-com/storybook",
    "@storybook/addon-docs",
    "@storybook/addon-a11y",
    "storybook/viewport",
  ],

  staticDirs: ["../public"],

  parameters: {
    nextjs: {
      appDirectory: true, // Enable Next.js App Router support
    },
  },

  webpackFinal: async (config) => {
    // Universal action mocking (if using server actions)
    const webpack = require('webpack');
    config.plugins.push(
      new webpack.NormalModuleReplacementPlugin(
        /\/action(?:\.js)?$/,
        path.resolve(__dirname, 'mocks/action.js')
      )
    );
    return config;
  }
};

export default config;
```

**Key features**:
- ✅ Stories pattern covers all source files
- ✅ A11y addon enabled
- ✅ Next.js App Router support
- ✅ Universal action mocking configured

---

### ✅ 2. Preview Configuration (`.storybook/preview.js`)

**Purpose**: Global story settings, decorators, and parameters

**Required settings**:
```javascript
import { INITIAL_VIEWPORTS } from 'storybook/viewport';
import '../src/app/globals.css';

const preview = {
  parameters: {
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/i,
      },
    },

    // A11y testing configuration
    a11y: {
      test: 'todo', // Show violations without failing tests
    },

    // Viewport configuration
    viewport: {
      viewports: INITIAL_VIEWPORTS,
    },

    // Next.js configuration
    nextjs: {
      appDirectory: true,
      navigation: {
        pathname: '/',
        query: {},
      },
    },

    // Layout
    layout: 'fullscreen',
  },

  // Default viewport (mobile-first)
  initialGlobals: {
    viewport: { value: 'mobile1', isRotated: false },
  },

  // Custom description banner decorator
  decorators: [
    (Story, context) => {
      const description = context.parameters?.description;

      return (
        <div>
          {description && (
            <div className="px-4 py-2 mb-2 bg-sky-50 border-b-1 border-sky-600 text-sm leading-relaxed text-slate-700">
              <span className='font-semibold'>Description: </span>{description}
            </div>
          )}
          <Story />
        </div>
      );
    },
  ],
};

export default preview;
```

**Key features**:
- ✅ A11y testing enabled
- ✅ Custom description banner
- ✅ Mobile-first viewport default
- ✅ Next.js App Router support
- ✅ Global CSS imported

**Reference**: [Custom Description Banner](documentation/custom-description-banner.md)

---

### ✅ 3. Test Runner Configuration (`.storybook/test-runner.js`)

**Purpose**: Configure automated testing with Playwright

**Required settings**:
```javascript
const { getStoryContext } = require('@storybook/test-runner');

module.exports = {
  // Parallel test execution
  workers: 2,

  async preVisit(page, context) {
    // Set consistent viewport
    await page.setViewportSize({ width: 1366, height: 768 });
  },

  async postVisit(page, context) {
    // Detect console errors
    const errors = await page.evaluate(() => {
      return window.__errors || [];
    });

    if (errors.length > 0) {
      console.warn(`⚠️  Console errors in ${context.id}:`, errors);
    }
  },

  // Playwright browser configuration
  use: {
    channel: 'chromium',
    actionTimeout: 10000,
    navigationTimeout: 30000,
  },

  // Retry configuration
  retries: 1,
};
```

**Key features**:
- ✅ Parallel execution (2 workers)
- ✅ Consistent viewport
- ✅ Console error detection
- ✅ Automatic retries

**Reference**:
- [Test Runner Config](testing/test-runner-config.md)
- [Test Runner Hooks](testing/test-runner-hooks.md)

---

### ✅ 4. Manager Configuration (`.storybook/manager.js`)

**Purpose**: Customize Storybook UI theme

**Required settings**:
```javascript
import { addons } from 'storybook/manager-api';
import { create } from 'storybook/theming/create';

const theme = create({
  base: 'light',
  brandTitle: 'SB / [Your Project Name]',
  // brandImage: 'https://your-logo-url.com/logo.png',
});

addons.setConfig({
  theme,
});
```

**Key features**:
- ✅ Custom branding
- ✅ Light theme

---

### ✅ 5. Universal Action Mock (`.storybook/mocks/action.js`)

**Purpose**: Mock Next.js server actions to prevent database errors

**Required implementation**:
```javascript
const { fn } = require('storybook/test');

console.log('🎭 Universal action mock loaded');

const createMockAction = (functionName) => {
  return fn()
    .mockImplementation(async (...args) => {
      console.log(`🎭 Mock ${functionName} called with:`, args);
      await new Promise(resolve => setTimeout(resolve, 1000));
      return {
        success: true,
        message: `Mock ${functionName} completed successfully`,
      };
    })
    .mockName(functionName);
};

const mockCache = {};

const handler = {
  get(target, prop) {
    if (typeof prop === 'string') {
      if (!mockCache[prop]) {
        mockCache[prop] = createMockAction(prop);
      }
      return mockCache[prop];
    }
    return undefined;
  }
};

module.exports = new Proxy({}, handler);
```

**Key features**:
- ✅ Dynamic mock creation
- ✅ Automatic caching
- ✅ Simulated API delays
- ✅ Works with any action name

**Reference**: [Mocking Actions](mocking/actions.md)

---

## Package.json Scripts

### Required Scripts

Add these to your `package.json`:

```json
{
  "scripts": {
    "storybook:dev": "storybook dev -p 6006",
    "storybook:build": "storybook build",
    "storybook:serve": "npx serve storybook-static -l 6006",
    "storybook:test": "test-storybook --verbose --junit",
    "storybook:test:ci": "concurrently -k -s first -n \"SB,TEST\" -c \"magenta,blue\" \"npm run storybook:build && npm run storybook:serve\" \"wait-on tcp:6006 && npm run storybook:test\"",
    "storybook:coverage": "node scripts/storybook-coverage.js",
    "storybook:validate": "node scripts/validate-stories.mjs"
  }
}
```

### Granular Test Scripts (Optional but Recommended)

Add feature-specific test scripts for faster development:

```json
{
  "scripts": {
    "storybook:test:ui": "test-storybook --json --outputFile test-results/ui.json src/components/ui",
    "storybook:test:components": "test-storybook --json --outputFile test-results/components.json src/components",
    "storybook:test:pages": "test-storybook --json --outputFile test-results/pages.json \"src/app/**\""
  }
}
```

**Reference**: [Testing Development Workflow](testing/testing-development-workflow.md)

---

## Scripts Setup

### ✅ 1. Coverage Script (`scripts/storybook-coverage.js`)

**Purpose**: Track which components have stories

**Setup**:
1. Create `scripts/` directory
2. Copy coverage script from reference project
3. Configure exclusion patterns for your project

**Key features**:
- Scans all `.jsx`/`.tsx` files
- Excludes test files, pages, layouts
- Generates console, markdown, and SVG badge reports
- Fails CI if coverage < 100%

**Reference**: [Coverage Script](testing/coverage-script.md)

---

### ✅ 2. Story Validation Script (`scripts/validate-stories.mjs`)

**Purpose**: Enforce story quality standards

**Setup**:
1. Create validation script (ESM)
2. Add to pre-commit hooks
3. Add to CI/CD pipeline

**Key checks**:
- ❌ Anti-pattern story names (Default, Primary, etc.)
- ✅ User Journey Pattern compliance
- ✅ Story descriptions present
- ✅ Title conventions followed
- ✅ ArgTypes naming (snake_case)

---

## Directory Structure

### Required Directories

```
project-root/
├── .storybook/
│   ├── main.js                 ✅ Main configuration
│   ├── preview.js              ✅ Preview configuration
│   ├── test-runner.js          ✅ Test runner configuration
│   ├── manager.js              ✅ UI theme configuration
│   └── mocks/
│       ├── action.js           ✅ Universal action mock
│       └── data/               ⚪ Optional: Centralized mock data
│           ├── users.json
│           ├── products.json
│           └── ...
├── scripts/
│   ├── storybook-coverage.js   ✅ Coverage tracking
│   └── validate-stories.mjs    ✅ Story validation
├── src/
│   └── **/*.stories.jsx        ✅ Story files co-located
└── package.json                ✅ Scripts configured
```

---

## Verification Steps

### 1. Start Storybook

```bash
npm run storybook:dev
```

**Verify**:
- ✅ Storybook loads at http://localhost:6006
- ✅ Stories appear in sidebar
- ✅ No console errors on startup
- ✅ Custom theme applied

---

### 2. Check Story Features

**Open any story and verify**:
- ✅ Description banner appears at top (if story has `parameters.description`)
- ✅ A11y panel shows accessibility checks
- ✅ Viewport switcher works
- ✅ Mobile viewport selected by default
- ✅ Server actions are mocked (check console for 🎭 messages)

---

### 3. Run Tests

```bash
npm run storybook:test:ci
```

**Verify**:
- ✅ Storybook builds successfully
- ✅ Server starts on port 6006
- ✅ Tests execute in parallel (2 workers)
- ✅ Tests pass or fail with clear output
- ✅ Console errors detected

---

### 4. Check Coverage

```bash
npm run storybook:coverage
```

**Verify**:
- ✅ Script runs without errors
- ✅ Console shows coverage percentage
- ✅ Markdown report generated in `dev_docs/storybook/`
- ✅ SVG badge generated
- ✅ Missing stories listed

---

### 5. Validate Stories (Optional)

```bash
npm run storybook:validate
```

**Verify**:
- ✅ Script scans all story files
- ✅ Reports anti-patterns
- ✅ Shows missing descriptions
- ✅ Checks title conventions

---

## Common Issues & Solutions

### Issue: Stories don't load

**Check**:
1. Story pattern in `main.js` matches your file structure
2. Stories export a `default` meta object
3. Next.js globals.css is imported in preview.js

---

### Issue: Server actions fail

**Check**:
1. Action mock in `.storybook/mocks/action.js` exists
2. Webpack plugin in `main.js` is configured
3. Console shows 🎭 mock messages

---

### Issue: Tests timeout

**Check**:
1. `test-runner.js` timeout values
2. Increase `actionTimeout` and `navigationTimeout`
3. Check for async operations without proper waiting

---

### Issue: A11y violations not showing

**Check**:
1. `@storybook/addon-a11y` installed
2. Addon listed in `main.js`
3. `a11y` parameter in `preview.js`
4. A11y panel visible in Storybook UI

---

### Issue: Description banner not showing

**Check**:
1. Decorator in `preview.js` configured
2. Story has `parameters.description` set
3. Decorator wraps `<Story />` correctly

---

## Quick Configuration Checklist

Use this for rapid auditing of existing Storybook setups:

- [ ] `.storybook/main.js` exists with correct framework
- [ ] `.storybook/preview.js` exists with custom decorator
- [ ] `.storybook/test-runner.js` exists with hooks
- [ ] `.storybook/manager.js` exists with theme
- [ ] `.storybook/mocks/action.js` exists
- [ ] `@storybook/addon-a11y` installed and configured
- [ ] `@storybook/test-runner` installed
- [ ] `playwright` installed
- [ ] npm scripts configured (dev, build, test, test:ci)
- [ ] Coverage script exists
- [ ] A11y testing enabled in preview
- [ ] Description banner decorator configured
- [ ] Mobile-first viewport default set
- [ ] Test runner parallel workers configured
- [ ] Console error detection in post-visit hook

---

## Next Steps After Setup

1. **Write your first story**: [User Journey Pattern](storybook-user-journey-pattern.md)
2. **Configure mock data**: [Mocking Data](mocking/data.md)
3. **Add play functions**: [Play Functions Basics](play-functions/basics.md)
4. **Organize stories**: [Title Convention](organization/title-convention.md)
5. **Run tests**: [Testing Development Workflow](testing/testing-development-workflow.md)

---

## Reference Documentation

### Configuration
- [Test Runner Config](testing/test-runner-config.md)
- [Test Runner Hooks](testing/test-runner-hooks.md)
- [Custom Description Banner](documentation/custom-description-banner.md)

### Patterns
- [User Journey Pattern](storybook-user-journey-pattern.md)
- [Story Naming](organization/story-naming.md)
- [Title Convention](organization/title-convention.md)

### Testing
- [Coverage Requirements](testing/coverage-requirements.md)
- [Coverage Script](testing/coverage-script.md)
- [Testing Development Workflow](testing/testing-development-workflow.md)

### Mocking
- [Mocking Actions](mocking/actions.md)
- [Mocking Data](mocking/data.md)

---

## Maintenance

**Review configuration**:
- When upgrading Storybook versions
- When adding new Next.js features
- When team reports Storybook issues
- Quarterly configuration audits

**Update this guide**:
- When discovering new best practices
- When configuration patterns change
- When adding new required addons

---

**Last Updated**: 2025-11-24
**Status**: Living Document
**Maintainer**: Engineering Standards Team
