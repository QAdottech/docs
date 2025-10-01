# Documentation Tasklist for QA.tech

**Created:** September 30, 2025  
**Last Updated:** October 1, 2025  
**Purpose:** Active tasklist and findings tracker for qatech-docs improvements

## 🎯 Active Tasks

### Priority 1: API Reference - CHAT Trigger Type ⚠️ **PENDING**

- **File:** `/api-reference/start-run.json`
- **Issue:** Trigger enum missing CHAT type
- **Fix:** Update enum to `["MANUAL", "API", "GITHUB", "SCHEDULE", "CHAT"]` + description + example
- **Status:** ⏳ Needs to be done

### Priority 2: Test Status Documentation (Optional)

- **File:** `/core-concepts/tests-and-results.mdx`
- **Issue:** Draft vs Active status not explicitly documented
- **Status:** ⏳ Low priority - check UI tooltips first

---

## ✅ Completed Tasks

- **Oct 1, 2025:** Parallel Execution Documentation - Added "Performance & Parallel Execution" section to `/core-concepts/test-plans.mdx` explaining automatic parallelization (up to ~100 concurrent agents), dependency-aware scheduling, and auto-scaling. Added cross-reference note in `/best-practices/running-tests.mdx`.
- **Oct 1, 2025:** IP Whitelisting CAPTCHA Clarification - Added service-agnostic section to `/configuration/ip-access-control.mdx` explaining NAT IPs work for reCAPTCHA, hCaptcha, AWS WAF, etc.

---

## 📊 Audit Findings (Sept 30, 2025)

### API Endpoints - Missing Documentation

#### POST `/api/projects/{id}/test-cases` - HIGH PRIORITY

**Purpose:** Programmatically create test cases via API  
**Auth:** Bearer token (project.apiToken)  
**Request Schema:**

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

**Response:** `{ success: true, testCase: { id, name, url } }`  
**Reference:** `apps/saas/src/app/api/projects/[projectUuid]/test-cases/README.md`

#### GET `/api/projects/{id}/runs/{shortId}` - HIGH PRIORITY

**Purpose:** Get test run status and result (CI/CD polling)  
**Auth:** Bearer token (project.apiToken)  
**Response:** `{ id: string, short_id: string, status: string, result: string }`

#### GET `/api/projects/{id}/metrics` - MEDIUM PRIORITY

**Purpose:** Retrieve comprehensive project test metrics  
**Auth:** Bearer token (project.apiToken)  
**Response:** `{ project_id, date_range: { from, to }, runs: { total, passed, failed, ... }, tests: { total, ... }, time_saved_minutes, ... }`  
**Reference:** `apps/saas/src/app/api/projects/[projectUuid]/metrics/README.md`

#### GET `/api/projects/{id}/badge.svg` - MEDIUM PRIORITY

**Purpose:** Generate status badge for embedding  
**Auth:** Token via query param `?token={uuid}`  
**Query Params:** `token` (required), `label?`, `style?` ('standard'|'flat'|'compact'), `showDate?`, `historyCount?`, `example?`  
**Response:** SVG image

#### POST `/api/projects/{id}/applications/{appId}/environments` - LOW PRIORITY

**Purpose:** Create new environment for application
**Auth:** Bearer token  
**Request:** `{ name: string (1-100 chars), url: string (valid URL), isPreview?: boolean }`

#### GET `/api/outbound-ips` - LOW PRIORITY

**Purpose:** Get list of QA.tech outbound IP addresses  
**Auth:** None (public endpoint)  
**Response:** `{ ipAddresses: string[] }`  
**Action:** Add to API reference section (already in IP docs)

### Browser Engine - Undocumented Features

#### HAR Recording & Playback - HIGH PRIORITY

**What:** `BrowserSessionRecordOptions` with `record`, `play`, `harFile`  
**Use Case:** Record once, replay tests without hitting live servers  
**Features:** har.zip format, streaming JSON parsing, full HAR 1.2 spec  
**Tests:** `iframe.test.ts`

#### SSH Tunnel Proxy - HIGH PRIORITY

**What:** `SshProxyServerInput` for testing internal/VPN apps  
**Package:** `@repo/ssh-tunnel-proxy`  
**Parameters:** host, user, privateKey, sshPort  
**Tests:** `ssh-tunnel-proxy.test.ts`

#### Advanced File Picker - HIGH PRIORITY

**What:** `window.showOpenFilePicker()` API support  
**Features:** Drag-and-drop, Shadow DOM-based fake picker UI, scrollable list, file type filtering, multiple selection  
**Providers:** SUPABASE, EXTERNAL_URL, DEV_LOCAL_PATH  
**Tests:** `show-open-file-picker.test.ts`, `file-picker-scroll.test.ts`

#### Video Recording - HIGH PRIORITY

**What:** ffmpeg-based via CDP screencast  
**Features:** Dynamic FPS (25 fps active, reduces when idle), WebM/VP8 format  
**Parameter:** `tracerOptions.recordVideo`  
**Tests:** `videoRecorder.test.ts`

#### Session State Management - HIGH PRIORITY

**What:** `storeOutputState` / `resumeOutputState` mutations  
**Features:** Full browser state (cookies, localStorage, sessionStorage, IndexedDB)  
**Tests:** `storeOutputState.test.ts`

#### CAPTCHA Handling - HIGH PRIORITY

**What:** Capsolver integration for Turnstile and FriendlyCaptcha  
**Features:** Auto-detects submit buttons, generates tokens  
**Location:** `apps/browser-engine/src/lib/browser/captcha/`  
**Note:** Currently enabled only for specific whitelisted projects

#### Device Preset System - HIGH PRIORITY

**Package:** `@repo/device-presets`  
**Features:** viewport, locale, timezone, user agent, colorScheme, forcedColors, reducedMotion, custom HTTP headers  
**Presets:** desktop (1280x800), mobile (375x667), tablet (768x1024)

#### Dialog Capture - HIGH PRIORITY

**What:** `captureDialogTypes` parameter in mutations  
**Features:** Auto-accept/reject alert/confirm/prompt/beforeunload, text capture, iframe support  
**Tests:** `alert-capture.test.ts`, `window-confirm.test.ts`

#### Page Readiness - HIGH PRIORITY

**Functions:** `waitForPageReady()`, `waitForFCP()`, `waitForStableNetwork()`, `waitForMutation()`

#### Medium Priority

- Console Log Capture, Network Body Capture, Ad Blocker Integration
- IndexedDB State Capture, Multi-Tab/Window Support
- Iframe Advanced Handling, Custom HTTP Headers, Screenshot Types

---

## 📌 Reference Notes

**Code Locations (Sept 30 Audit):**

- CHAT Trigger: `supabase/migrations/20250929124415_add_chat_trigger_type.sql`, `packages/types/src/run.ts`
- Test Filtering UI: `apps/saas/src/app/dashboard/p/[project]/scenarios/components/ProjectTestCaseIsEnabledSwitch.tsx`
- Browser Engine GraphQL: `apps/browser-engine/src/graphql/*.ts`, 130 test files analyzed
- MCP Tools: `packages/mcp/src/agents/`, `packages/mcp/src/tools/`

**Quick Audit Command:**

```bash
cd /Users/pridiuksson/Code/qatech
git log --since="1 week ago" --oneline -- "apps/saas/src/app/api/**/*.ts"
git log --since="1 week ago" --oneline -- "packages/types/**/*.ts"
git log --since="1 week ago" --oneline -- "supabase/migrations/*.sql"
```
