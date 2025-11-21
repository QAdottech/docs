# Documentation Tasklist for QA.tech

**Created:** September 30, 2025  
**Last Updated:** October 2, 2025  
**Purpose:** Active tasklist and findings tracker for qatech-docs improvements

## 🎯 Active Tasks - Prioritized & Verified

### 🔴 CRITICAL PRIORITY (Customer-Facing, Missing Docs)

#### 1. GitHub App Integration (Separate from GitHub Actions) ⚠️

- **Target File:** `/integrations/github-app.mdx` (new file)
- **Why Critical:** Distinct customer-facing integration separate from GitHub Actions
- **What's Missing:** Complete integration guide for PR-based testing
- **Key Points:**
  - Distinct from GitHub Actions workflow integration
  - Auto-runs exploratory + regression tests on PR creation/updates
  - Screenshot available: `/assets/images/integrations/screenshot-review.jpg`
  - Different setup flow than GitHub Actions (OAuth App vs Actions workflow)
- **Code Reference:** `apps/saas/src/app/p/[project]/settings/integrations/[integrationId]/components/GitHubAppIntegrationSettings.tsx`
- **Action:** Create new integration guide; explain OAuth setup, PR triggers, review flow, permissions
- **Status:** ⚠️ **PENDING**

#### 2. Prometheus Metrics Integration ⚠️

- **Target File:** `/integrations/prometheus.mdx` (new file)
- **Why Critical:** New customer-facing integration with UI setup page
- **What's Missing:** Complete integration guide with Prometheus scrape configuration
- **Key Points:**
  - Prometheus-compatible scrape endpoint for monitoring
  - Metrics: runs (total/passed/failed/skipped), tests, user impact, deployments
  - 30s scrape interval recommended
  - Uses same API token as other integrations
  - Includes prometheus.yml config example in UI
- **Code Reference:** `apps/saas/src/app/p/[project]/settings/integrations/[integrationId]/components/MetricsIntegrationSettings.tsx`
- **Action:** Create integration guide with setup, config examples, available metrics reference, Grafana dashboard examples
- **Cross-reference:** GET `/api/projects/{id}/metrics` endpoint (see API section)
- **Status:** ⚠️ **PENDING**

#### 3. API Reference - CHAT Trigger Type ⚠️

- **Target File:** `/api-reference/start-run.json`
- **Why Critical:** Missing trigger type in public API docs
- **Issue:** Trigger enum missing CHAT type
- **Fix:** Update enum to `["MANUAL", "API", "GITHUB", "SCHEDULE", "CHAT"]`
- **Action:** Add CHAT description and example showing chat-initiated runs
- **Status:** ⚠️ **PENDING**

### 🟡 HIGH PRIORITY (Customer-Facing APIs)

#### 4. POST `/api/projects/{id}/test-cases` - Test Case Creation API

- **Target File:** `/api-reference/test-case-creation.mdx`
- **Why High:** Core API for programmatic test case creation
- **Current Status:** File exists but check if complete
- **Request Schema:**

```typescript
{
  name: string (1-80 chars, required),
  goal: string (required),
  expectedResult?: string,
  resumeFromDependencyProjectTestCaseId?: uuid | null,
  waitForDependenciesProjectTestCaseIds?: uuid[],
  configIds?: uuid[],
  applicationId: uuid (required),
  scenarioId?: uuid | null
}
```

- **Response:** `{ success: true, testCase: { id, name, url } }`
- **Code Reference:** `apps/saas/src/app/api/projects/[projectUuid]/test-cases/README.md`
- **Action:** Verify existing doc is complete; add dependency examples
- **Status:** ⏳ Verify & update if needed

#### 5. GET `/api/projects/{id}/runs/{shortId}` - Run Status API

- **Target File:** `/api-reference/get-run-status.mdx` (new)
- **Why High:** Critical for CI/CD polling and status monitoring
- **Purpose:** Get test run status and result
- **Auth:** Bearer token (project.apiToken)
- **Response:** `{ id: string, short_id: string, status: string, result: string }`
- **Action:** Create endpoint reference with polling examples
- **Status:** ⏳ Needs to be done

#### 6. GET `/api/projects/{id}/metrics` - Metrics API Endpoint

- **Target File:** Update `/api-reference/introduction.mdx` or create `/api-reference/metrics.mdx`
- **Why High:** Supports Prometheus integration (Item #2)
- **Purpose:** Retrieve comprehensive project test metrics
- **Auth:** Bearer token (project.apiToken)
- **Response Schema:**

```typescript
{
  project_id: string,
  date_range: { from: string, to: string },
  runs: { total, passed, failed, skipped, ... },
  tests: { total, ... },
  time_saved_minutes: number,
  ...
}
```

- **Code Reference:** `apps/saas/src/app/api/projects/[projectUuid]/metrics/README.md`
- **Action:** Create API reference with response schema, examples
- **Status:** ⏳ Needs to be done

### 🟢 MEDIUM PRIORITY (UX Improvements & Doc Updates)

#### 7. Device Presets - Accessibility Features (UPDATE EXISTING) ⚠️

- **Target File:** `/test-features/device-presets.mdx` (UPDATE)
- **Why Medium:** Missing documentation for accessibility emulation features
- **Current Status:** ✓ Doc exists but INCOMPLETE
- **What's Missing:** Three accessibility/emulation features:
  - **colorScheme** (light, dark, no-preference) - Emulates 'prefers-color-scheme' media feature
  - **forcedColors** (active, none) - High contrast mode for accessibility
  - **reducedMotion** (reduce, no-preference) - Emulates 'prefers-reduced-motion' for users sensitive to animations
- **Evidence:** All three are:
  - ✓ In device preset schema (`packages/device-presets/src/index.ts`)
  - ✓ Displayed in UI hover cards (`DevicePresetHoverCard.tsx`)
  - ✓ `colorScheme` has UI selector (`ColorSchemeSelector.tsx`)
- **Action:** Add section "Accessibility & Emulation Features" covering these three settings with use cases
- **Status:** ⏳ Update needed

#### 8. Ephemeral Test Cases in Chat

- **Target File:** `/best-practices/creating-tests.mdx` (add section)
- **Why Medium:** Advanced chat feature, beneficial for power users
- **What's Missing:** Documentation of ephemeral test case mode
- **Key Points:**
  - Test cases auto-generate and run without confirmation
  - Useful for exploratory testing and long chains
  - Automatically added to test case library
  - Opt-in via conversation flow
- **Code Reference:** `packages/mcp/src/tools/suggest-test-cases.ts` (isEphemeral flag)
- **Action:** Add "Ephemeral Mode for Exploratory Testing" section to chat docs
- **Status:** ⏳ Needs to be done

#### 9. Inline Test Case Suggestions (Chat UX)

- **Target File:** `/best-practices/creating-tests.mdx` (update section)
- **Why Medium:** UX change, users will discover naturally
- **What Changed:** Refactored test suggestion UI in chat
- **Key Points:**
  - Individual test case generation on-demand (not batch-only)
  - Real-time status tracking (started/finished/failed)
  - Visual cards for each suggestion
  - Accept/reject individual suggestions
  - Extended with automatic deduplication
- **Code Reference:** `apps/saas/src/components/Chat/messages/assistant/tools/suggestions/InlineToolCallSuggestTestCases.tsx`
- **Action:** Update chat documentation with new UX flow, add screenshots
- **Status:** ⏳ Needs to be done

#### 10. GET `/api/projects/{id}/badge.svg` - Status Badge API

- **Target File:** Update `/integrations/status-badges.mdx`
- **Why Medium:** Existing integration docs may need API details
- **Purpose:** Generate status badge SVG for embedding
- **Auth:** Token via query param `?token={uuid}`
- **Query Params:** `token` (required), `label?`, `style?` ('standard'|'flat'|'compact'), `showDate?`, `historyCount?`, `example?`
- **Action:** Verify existing docs include all parameters and examples
- **Status:** ⏳ Verify & update if needed

#### 11. Netlify Integration Page

- **Target File:** Update navigation to include Netlify or create `/configuration/netlify.mdx`
- **Why Medium:** Now first-class integration in UI
- **Current Status:** May exist in core-concepts/preview-environments.mdx
- **Key Points:** "Trigger tests after a successful deploy to Netlify"
- **Code Reference:** `NETLIFY_INTEGRATION_CONFIG` in `INTEGRATION_CONFIGS.tsx`
- **Action:** Verify if dedicated page needed or if preview-environments covers it
- **Status:** ⏳ Investigate & decide

### 🔵 LOW PRIORITY (Nice-to-Have)

#### 12. POST `/api/projects/{id}/applications/{appId}/environments` - Create Environment API

- **Why Low:** Advanced API, most users create via UI
- **Purpose:** Create new environment for application
- **Auth:** Bearer token
- **Request:** `{ name: string (1-100 chars), url: string (valid URL), isPreview?: boolean }`
- **Status:** ⏳ Low priority

#### 13. GET `/api/outbound-ips` - Outbound IPs API

- **Why Low:** Already covered in IP docs content
- **Purpose:** Get list of QA.tech outbound IP addresses
- **Auth:** None (public endpoint)
- **Action:** Add to API reference as public utility endpoint
- **Status:** ⏳ Low priority

#### 14. Test Status Documentation (Draft vs Active)

- **Target File:** `/core-concepts/tests-and-results.mdx`
- **Why Low:** Check UI tooltips first
- **Issue:** Draft vs Active status not explicitly documented
- **Status:** ⏳ Low priority - verify if needed

---

## ✅ Completed Tasks

- **Nov 21, 2025:** Start Run API Slack Overrides — Updated `/api-reference/start-run.mdx` and `/api-reference/start-run.json` to describe the new `notifications` payload for overriding Slack channels per run (PR #5889 / QAT-4475). Documented request schema, notifyOn behavior (`slack/run-created` + `slack/run-finish` inngest functions), validation errors when Slack is not configured or the channel ID is inaccessible, and the current sshTunnelProxy limitation. Verified implementation in `apps/saas/src/app/api/projects/[projectUuid]/runs/route.ts`, `apps/saas/src/core/inngest/functions/slackRunCreated.ts`, `apps/saas/src/core/inngest/functions/slackRunFinish.ts`, and `apps/saas/src/core/inngest/helpers/slackUtils.ts`.
- **Nov 4, 2025:** API Calls Feature - Added `/test-features/api-calls.mdx` documenting the API calls feature introduced in PR #5752. Covers configuration, HTTP methods, response handling, use cases, and limitations. Includes critical warning about SSH Tunnel Proxy incompatibility (tracked in Linear QAT-5136). Documented that API calls are configured as configs, require valid configuration to execute, and currently do not work with SSH Tunnel Proxy. Added to navigation in `mint.json`. Verified implementation in `apps/agent-runner/src/lib/shared/tools/callApi.ts`, `packages/agent-event-tools/src/tools.ts` (callApi tool), and `packages/test-configs/src/config-schemas/api-call.ts`.
- **Oct 27, 2025:** Dynamic Navigation Capability - Added `/test-features/dynamic-navigation.mdx` documenting the agent's ability to navigate to URLs dynamically during test execution (QAT-4258). Covers three navigation modes: full URLs, relative URLs (e.g., `/dashboard`, `../settings`), and clipboard URLs. Includes use cases, supported modes table, and example scenarios. Added to navigation in `mint.json`. Deployed for user discoverability and chat agent context. Verified implementation in `apps/agent-runner/src/lib/agents/zigZaggety/reason/tools/browser/navigate.ts` and `packages/agent-event-tools/src/tools.ts` (browser tool with navigate action).
- **Oct 23, 2025:** Cross-Application Dependency Constraints - Added "Application and Environment Constraints" section to `/core-concepts/dependencies.mdx` explaining that browser state (cookies, login sessions) cannot transfer across different domains due to browser security. Includes comparison table of what works vs. doesn't work, guidance on using Wait For vs. Resume From dependencies across applications, and practical examples for multi-service workflows. Added cross-reference note in `/core-concepts/applications-and-environments.mdx` under "Multi-Service Products" section. Verified implementation in `apps/hackoffice/src/app/api/blueprints/outputStateUtils.ts` (environment_id filtering) and `createBlueprintForTests.ts` (application_id → environment_id mapping).
- **Oct 6, 2025:** Output State Time Window & Stale Data Fix - Corrected time window from 1 hour to 6 hours in `/core-concepts/output-state-optimization.mdx` and `/core-concepts/dependencies.mdx`. Added concise troubleshooting entry for stale output data (OTP codes, tokens) with "Run w. Dependencies" solution. Clarified that both browser state AND output data are preserved. Added "Run with Dependencies" section to `/best-practices/running-tests.mdx` explaining when to use each option.
- **Oct 2, 2025:** HTTP Basic Authentication Documentation - Added detailed explanation of "Use for Basic Auth" checkbox in Username + Password config section of `/core-concepts/configs.mdx`. Includes accordion with use cases (staging environments, browser auth popups, corporate proxies), distinction from form-based login, and implementation details (RFC 7617).
- **Oct 2, 2025:** Documentation Backlog Audit - Comprehensive audit completed. Verified 5 features already have docs (SSH Tunnel, Dialog Handling, Device Presets, Output State, File Uploads). Identified Device Presets needs update for accessibility features. Removed 15 internal/not-customer-facing items.
- **Oct 1, 2025:** Parallel Execution Documentation - Added "Performance & Parallel Execution" section to `/core-concepts/test-plans.mdx` explaining automatic parallelization (up to ~100 concurrent agents), dependency-aware scheduling, and auto-scaling. Added cross-reference note in `/best-practices/running-tests.mdx`.
- **Oct 1, 2025:** IP Access Control - Merged comprehensive IP access control guidance combining CAPTCHA clarification with CDN/firewall configuration from PR #12. Includes Cloudflare AI Crawl Control (recommended), AWS CloudFront guidance, troubleshooting section, and expanded platform list. Supersedes [PR #12](https://github.com/QAdottech/docs/pull/12).

---

## 🗑️ REMOVED FROM BACKLOG (Not Customer-Facing / Already Documented)

### Already Documented ✅ (No Update Needed)

These features ARE customer-facing AND docs are current:

1. **SSH Tunnel Proxy** ✅ - Documented in `/configuration/ssh-tunnel.mdx` - Comprehensive, up-to-date
2. **Dialog Handling** ✅ - Documented in `/test-features/dialog-handling.mdx` - Covers alert, confirm, prompt, beforeunload
3. **Session State Management** ✅ - Documented in `/core-concepts/output-state-optimization.mdx` - Covers dependency output states
4. **File Uploads** ✅ - Documented in `/test-features/file-uploads.mdx` - Covers default files, custom uploads, configs

### Already Documented ⚠️ (Needs Update)

5. **Device Presets** ⚠️ - Documented in `/test-features/device-presets.mdx` BUT missing accessibility features (colorScheme, forcedColors, reducedMotion) - **Moved to Medium Priority #7**

### Internal/Not Customer-Facing ❌

These are internal development/testing features:

1. **HAR Recording & Playback** ❌ - Internal testing tool, not exposed to customers
2. **Video Recording** ❌ - Internal debugging feature, not exposed in SaaS UI
3. **CAPTCHA Handling** ❌ - Hardcoded for specific whitelisted projects only (see `CAPSOLVER_ENABLED_PROJECT_IDS`)
4. **Page Readiness Functions** ❌ - Internal browser engine implementation details
5. **Advanced File Picker** ❌ - Internal browser API support, covered by basic file upload docs
6. **Console Log Capture** ❌ - Internal debugging feature
7. **Network Body Capture** ❌ - Internal debugging feature
8. **Ad Blocker Integration** ❌ - Internal feature
9. **IndexedDB State Capture** ❌ - Internal, part of Session State Management
10. **Multi-Tab/Window Support** ❌ - Internal browser engine capability
11. **Iframe Advanced Handling** ❌ - Internal browser engine capability
12. **Custom HTTP Headers** ❌ - Documented in Device Presets (users can add via UI)
13. **Screenshot Types** ❌ - Internal debugging feature
14. **Get Applications MCP Tool** ❌ - Internal AI agent tool, not customer-facing API
15. **Pull Request Debugger** ❌ - Internal troubleshooting UI, not a feature to document

---

## 📌 Reference Notes

**Verification Process (Oct 2 Audit):**

For each "already documented" feature, I:

1. ✓ Read the existing documentation file
2. ✓ Checked the code implementation in monorepo main
3. ✓ Verified feature availability in UI/API
4. ✓ Confirmed docs match current implementation

**Docs Verified as Current:**

- SSH Tunnel: `/configuration/ssh-tunnel.mdx` - Comprehensive 235-line guide
- Dialog Handling: `/test-features/dialog-handling.mdx` - Covers all dialog types
- Output State: `/core-concepts/output-state-optimization.mdx` - Complete feature docs
- File Uploads: `/test-features/file-uploads.mdx` - Basic file upload functionality

**Docs Needing Update:**

- Device Presets: `/test-features/device-presets.mdx` - Missing colorScheme, forcedColors, reducedMotion

**Code Locations (Sept 30 Audit):**

- CHAT Trigger: `supabase/migrations/20250929124415_add_chat_trigger_type.sql`, `packages/types/src/run.ts`
- Browser Engine GraphQL: `apps/browser-engine/src/graphql/*.ts`, 130 test files analyzed
- MCP Tools: `packages/mcp/src/agents/`, `packages/mcp/src/tools/`

**Code Locations (Oct 2 Audit - Main Branch Merge):**

- GitHub App Integration: `apps/saas/src/app/p/[project]/settings/integrations/[integrationId]/components/GitHubAppIntegrationSettings.tsx`
- Prometheus Metrics: `apps/saas/src/app/p/[project]/settings/integrations/[integrationId]/components/MetricsIntegrationSettings.tsx`
- CAPTCHA (Internal Only): `apps/browser-engine/src/lib/browser/captcha/index.ts` - See `CAPSOLVER_ENABLED_PROJECT_IDS` (hardcoded single project)
- Ephemeral Tests: `packages/mcp/src/tools/suggest-test-cases.ts` (isEphemeral flag)
- Inline Test Suggestions: `apps/saas/src/components/Chat/messages/assistant/tools/suggestions/InlineToolCallSuggestTestCases.tsx`
- Device Presets Schema: `packages/device-presets/src/index.ts` - colorScheme, forcedColors, reducedMotion in schema
- Device Presets UI: `apps/saas/src/app/p/[project]/settings/device-presets/components/advanced/ColorSchemeSelector.tsx`
- Integration Configs: `apps/saas/src/app/p/[project]/settings/integrations/[integrationId]/components/INTEGRATION_CONFIGS.tsx`
- Merge Details: 176 files changed (+3,714/-3,045 lines)

**Quick Audit Commands:**

```bash
# Check recent API changes
cd /Users/pridiuksson/Code/qatech
git log --since="1 week ago" --oneline -- "apps/saas/src/app/api/**/*.ts"

# Check type changes
git log --since="1 week ago" --oneline -- "packages/types/**/*.ts"

# Check DB migrations
git log --since="1 week ago" --oneline -- "supabase/migrations/*.sql"

# Check if feature is customer-facing (look for UI references)
rg "featureName" apps/saas/src --type tsx

# Verify accessibility features in device presets
rg "colorScheme|forcedColors|reducedMotion" packages/device-presets/src
```

**Prioritization Criteria:**

1. **Critical** - Customer-facing, missing entirely, new integration/API
2. **High** - Customer-facing API, incomplete docs, core functionality
3. **Medium** - UX improvements, advanced features, doc updates, secondary integrations
4. **Low** - Edge cases, rarely used, nice-to-have

---

## 📊 Summary

**Total Items:** 14 active tasks  
**Critical Priority:** 3 items  
**High Priority:** 3 items  
**Medium Priority:** 5 items (includes 1 doc update)  
**Low Priority:** 3 items  
**Already Documented:** 4 items ✅ (verified current)  
**Needs Update:** 1 item ⚠️ (Device Presets)  
**Internal/Removed:** 15 items ❌

**Focus First:**

1. Items 1-3 (GitHub App, Prometheus, CHAT trigger) for maximum customer impact
2. Item 7 (Device Presets update) - Quick win, existing doc just needs accessibility section

**Next Audit:** Review after next major merge to main (or monthly)
