# Conditional Deployment Guide

## How It Works

The release workflow now intelligently decides what to deploy based on what changed.

### Logic

```
On Release Published:
  ├─ Check if package.json changed
  │
  ├─ If package.json CHANGED:
  │  ├─ ✅ Build library
  │  ├─ ✅ Publish to npm
  │  └─ ✅ Deploy website
  │
  └─ If package.json UNCHANGED:
     ├─ ⏭️ Skip npm publish
     └─ ✅ Deploy website only
```

## Use Cases

### 1. Website-Only Changes

**Scenario:** You updated styling, filters, layout, etc.

**Steps:**
```bash
# Make your website changes
vim website/template.html
vim website/build.ts

# Commit and push
git add .
git commit -m "feat: add sorting by address count"
git push

# Create release (no version bump needed!)
git tag v0.0.9-web1
git push origin v0.0.9-web1
# Or use GitHub UI to create release
```

**Result:**
- ⏭️ npm publish: **Skipped**
- ✅ Website: **Deployed**
- 🎉 No unnecessary package bump!

### 2. Package Changes

**Scenario:** You updated library code, types, or package metadata.

**Steps:**
```bash
# Make your library changes
vim src/facilitators/new-facilitator.ts

# Bump version
bun version patch

# Push
git add .
git commit -m "feat: add new facilitator"
git push && git push --tags

# Create release
```

**Result:**
- ✅ npm publish: **Published**
- ✅ Website: **Deployed**
- 📦 New package version available

### 3. Both Changed 🚀

**Scenario:** You updated both library and website.

**Steps:**
```bash
# Make changes to both
vim src/facilitators/facilitator.ts
vim website/template.html

# Bump version (because library changed)
bun version patch

# Push and release
git push && git push --tags
```

**Result:**
- ✅ npm publish: **Published**
- ✅ Website: **Deployed**
- 🎯 Everything updated!

## How Detection Works

### Change Detection

The workflow compares the current commit with the previous one:

```bash
git diff HEAD^ HEAD --name-only | grep -q "^package.json$"
```

**Detects:**
- Version changes in package.json
- Dependency updates
- Package metadata changes
- Any package.json modifications

**Doesn't Detect:**
- Changes to `src/` files
- Changes to `website/` files
- Changes to other files
