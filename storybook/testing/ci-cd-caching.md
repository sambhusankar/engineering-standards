# CI Caching Strategies

Caching strategies to speed up CI builds for Storybook testing.

## Why Cache?

Caching prevents re-downloading/rebuilding unchanged dependencies on every CI run:

**Without caching**: 4+ minutes per build
**With caching**: ~1.5 minutes per build

## Cache Dependencies

Cache `node_modules` to speed up npm install:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'
```

**Benefit**: Faster installs by caching `node_modules`.

**Cache invalidation**: Automatically when `package-lock.json` changes.

## Cache Playwright Browsers

Playwright downloads Chromium on first install. Cache it:

```yaml
- name: Cache Playwright Browsers
  uses: actions/cache@v4
  with:
    path: ~/.cache/ms-playwright
    key: ${{ runner.os }}-playwright-${{ hashFiles('**/package-lock.json') }}

- name: Install Playwright Browsers
  run: npx playwright install --with-deps chromium
```

**Benefit**: Avoid re-downloading ~200MB Chromium on every run.

**Cache key**: Tied to `package-lock.json` - invalidates when Playwright version changes.

## Cache Storybook Build

If stories haven't changed, skip rebuild:

```yaml
- name: Cache Storybook Build
  uses: actions/cache@v4
  with:
    path: storybook-static
    key: ${{ runner.os }}-storybook-${{ hashFiles('src/**/*.stories.*') }}

- name: Build Storybook
  if: steps.cache-storybook.outputs.cache-hit != 'true'
  run: npm run storybook:build
```

**Benefit**: Skip 60-second build if stories haven't changed.

**Cache key**: Tied to story files - invalidates when any story changes.

**Limitation**: Doesn't detect changes in Storybook config files (`.storybook/*`). For stricter caching:

```yaml
key: ${{ runner.os }}-storybook-${{ hashFiles('src/**/*.stories.*', '.storybook/**') }}
```

## Complete Caching Example

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Setup Node.js
    uses: actions/setup-node@v4
    with:
      node-version: '20'
      cache: 'npm'

  - name: Install dependencies
    run: npm ci

  - name: Cache Playwright Browsers
    uses: actions/cache@v4
    with:
      path: ~/.cache/ms-playwright
      key: ${{ runner.os }}-playwright-${{ hashFiles('**/package-lock.json') }}

  - name: Install Playwright Browsers
    run: npx playwright install --with-deps chromium

  - name: Cache Storybook Build
    id: cache-storybook
    uses: actions/cache@v4
    with:
      path: storybook-static
      key: ${{ runner.os }}-storybook-${{ hashFiles('src/**/*.stories.*') }}

  - name: Build Storybook
    if: steps.cache-storybook.outputs.cache-hit != 'true'
    run: npm run storybook:build

  - name: Serve and Test
    run: npm run storybook:serve & wait-on tcp:6006 && npm run storybook:test
```

## Cache Hit Rates

Track cache effectiveness in GitHub Actions UI:

- **Dependencies**: ~95% hit rate (changes on package updates)
- **Playwright**: ~98% hit rate (changes rarely)
- **Storybook build**: ~70% hit rate (changes when stories modified)

## Other CI Platforms

### GitLab CI

```yaml
cache:
  paths:
    - node_modules/
    - .cache/ms-playwright/
```

### CircleCI

```yaml
- restore_cache:
    keys:
      - v1-deps-{{ checksum "package-lock.json" }}
- save_cache:
    key: v1-deps-{{ checksum "package-lock.json" }}
    paths:
      - node_modules
```

## Related Notes

- [GitHub Actions Integration](./ci-cd-github-actions.md) - Basic workflow setup
- [CI Optimization](./ci-cd-optimization.md) - Performance tuning
- [CI Troubleshooting](./ci-cd-troubleshooting.md) - Common issues
