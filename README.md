<div align="center">
  <h1>requo</h1>
  <p><strong>Local-first, Git-friendly API client</strong></p>
  <p>A fast, private alternative to Postman and Insomnia — your collections live on disk as plain files, version-controlled with the rest of your project.</p>

  <p>
    <a href="https://github.com/yanislavm/requo-releases/releases/latest">
      <img src="https://img.shields.io/github/v/release/yanislavm/requo-releases?label=latest&color=blue" alt="Latest release">
    </a>
    <img src="https://img.shields.io/badge/platform-macOS%20%7C%20Windows%20%7C%20Linux-lightgrey" alt="Platforms">
    <img src="https://img.shields.io/badge/license-MIT-green" alt="License">
  </p>

  <img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/overview.png" alt="requo main window" width="900">
</div>

---

## Table of contents

- [Why requo?](#why-requo)
- [Install](#install)
- [Quick start](#quick-start)
- [HTTP requests](#http-requests)
- [Authentication](#authentication)
- [Collections](#collections)
- [Environments & variables](#environments--variables)
- [Dynamic variables](#dynamic-variables)
- [Scripts](#scripts)
- [Variable extraction](#variable-extraction)
- [Response viewer](#response-viewer)
- [Response timing](#response-timing)
- [Collection runner & test suites](#collection-runner--test-suites)
- [gRPC](#grpc)
- [WebSocket](#websocket)
- [Server-sent events (SSE)](#server-sent-events-sse)
- [MQTT](#mqtt)
- [Load testing](#load-testing)
- [Mock server](#mock-server)
- [AI assistant](#ai-assistant)
- [Secret vaults](#secret-vaults)
- [Import & export](#import--export)
- [Git integration](#git-integration)
- [Cookie jar](#cookie-jar)
- [History](#history)
- [Settings reference](#settings-reference)
- [Keyboard shortcuts](#keyboard-shortcuts)
- [Server mode (headless)](#server-mode-headless)
- [Source code](#source-code)

---

## Why requo?

| | requo | Postman | Insomnia |
|---|---|---|---|
| **Stores data** | Plain files on disk | Postman cloud | Local + cloud |
| **Git-friendly** | Yes — commit collections alongside code | No | Partial |
| **No account needed** | Yes | No | No |
| **Open source** | Yes | No | Partial |
| **Offline-first** | Yes | Limited | Yes |
| **Bruno compatible** | Yes — open `.bru` collections in-place | No | No |

---

## Install

### macOS — Homebrew (recommended)

```bash
brew tap yanislavm/requo
brew install --cask requo
```

### macOS — Direct download

Download the latest `.dmg` from the [Releases](https://github.com/yanislavm/requo-releases/releases/latest) page. Open it, drag **requo.app** to Applications.

> The DMG is signed and notarized by Apple — no Gatekeeper warnings.

### Windows

Download `requo-*-windows-amd64.zip` from [Releases](https://github.com/yanislavm/requo-releases/releases/latest), extract, and run `requo.exe`.

### Linux

Download `requo-*-linux-amd64.tar.gz` from [Releases](https://github.com/yanislavm/requo-releases/releases/latest):

```bash
tar -xzf requo-*-linux-amd64.tar.gz
./requo-*-linux-amd64
```

### Verify a download

SHA-256 checksums are published alongside each release in `checksums.txt`.

```bash
# macOS / Linux
shasum -a 256 -c checksums.txt

# Windows (PowerShell)
Get-FileHash requo-*-windows-amd64.zip -Algorithm SHA256
```

---

## Quick start

1. **Open requo** and click **+** → **New Collection**. Give it a name and choose a folder — requo creates a `requo.json` manifest there and nothing else.
2. Right-click the collection and choose **New Request**. A new tab opens with a blank request.
3. Enter a URL in the address bar and press **Send** (or **⌘↵** / **Ctrl+Enter**).
4. Save the request to your collection with **⌘S** / **Ctrl+S**.
5. Create an **Environment** to store a `baseUrl` variable. Reference it as `{{baseUrl}}` anywhere in the request — URL, headers, body, or auth fields.
6. Press **?** to open the keyboard shortcuts reference.

---

## HTTP requests

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/request-editor.png" alt="Request editor" width="800">

### Methods

GET · POST · PUT · PATCH · DELETE · HEAD · OPTIONS

### URL bar

The URL bar is a code editor that resolves `{{variables}}` live as you type. Resolved variables are highlighted in **green**; unresolved or empty ones in **orange**.

Query parameters can be managed as a table below the URL bar — enable/disable individual parameters with the toggle on each row.

### Headers

Add headers as key/value pairs. Each row has an enable/disable toggle so you can keep headers around without sending them.

### Request body

| Mode | Content-Type sent | Description |
|------|------------------|-------------|
| **None** | — | No body |
| **JSON** | `application/json` | JSON editor with syntax highlighting and auto-prettify |
| **Text** | `text/plain` | Plain text |
| **Form (URL-encoded)** | `application/x-www-form-urlencoded` | Key/value table, URL-encoded |
| **Multipart** | `multipart/form-data` | Key/value + file fields |
| **GraphQL** | `application/json` | GraphQL editor with a separate variables panel |

### Per-request overrides

These override the global settings for individual requests, and are stored alongside the request on disk:

| Field | Type | Description |
|-------|------|-------------|
| `timeoutMs` | integer | Request timeout in milliseconds (0 = global default) |
| Follow redirects | boolean | Per-request redirect behaviour |
| Disable cookies | boolean | Skip the cookie jar for this request |

---

## Authentication

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/auth-editor.png" alt="Auth editor" width="700">

Auth settings live in the **Auth** tab of every request. They are stored in the collection on disk and resolved at send time — `{{variables}}` work in every auth field.

### None

No authentication header is added.

### Bearer token

Sends `Authorization: Bearer <token>`.

| Field | Description |
|-------|-------------|
| `token` | The bearer token value |

### Basic auth

Sends `Authorization: Basic <base64(username:password)>`.

| Field | Description |
|-------|-------------|
| `username` | Username |
| `password` | Password (supports `{{secrets}}`) |

### API key

Injects a custom header or query parameter.

| Field | Description |
|-------|-------------|
| `key` | Header or parameter name |
| `value` | Key value |
| `in` | `header` (default) or `query` |

### OAuth 2.0 — Client Credentials

Fetches a token from `tokenUrl` using the `client_credentials` grant and caches it until it expires (minus a 30-second buffer). On expiry the token is refreshed automatically.

| Field | Description |
|-------|-------------|
| `tokenUrl` | OAuth token endpoint |
| `clientId` | Client ID |
| `clientSecret` | Client secret |
| `scope` | Optional space-separated scopes |

### OAuth 2.0 — Resource Owner Password

Same caching behaviour as Client Credentials.

| Field | Description |
|-------|-------------|
| `tokenUrl` | OAuth token endpoint |
| `clientId` | Client ID |
| `clientSecret` | Client secret |
| `username` | Resource owner username |
| `password` | Resource owner password |
| `scope` | Optional scopes |

### OAuth 2.0 — Authorization Code + PKCE

Designed for interactive desktop flows. When you click **Authorize**, requo:

1. Generates a cryptographic code verifier and S256 code challenge.
2. Starts a temporary local HTTP listener on a random port.
3. Opens your system browser to `authUrl` with the PKCE parameters.
4. Captures the redirect to `http://127.0.0.1:<port>/callback`, extracts the code.
5. Exchanges the code for an access token + refresh token.
6. Caches the token; auto-refreshes using the refresh token before expiry.

| Field | Description |
|-------|-------------|
| `authUrl` | Authorization endpoint (browser redirect target) |
| `tokenUrl` | Token endpoint for code exchange and refresh |
| `clientId` | Client ID |
| `clientSecret` | Optional client secret (public clients may omit) |
| `scope` | Optional scopes |
| `redirectUri` | Defaults to `http://127.0.0.1:<random-port>/callback` |

### AWS Signature Version 4

Signs the request using the AWS SigV4 algorithm. Supports optional session tokens for temporary credentials.

| Field | Description |
|-------|-------------|
| `region` | AWS region (e.g. `us-east-1`) |
| `service` | AWS service name (e.g. `execute-api`) |
| `accessKeyId` | AWS access key ID |
| `secretAccessKey` | AWS secret access key |
| `sessionToken` | Optional session token (for STS/assumed roles) |

### Digest auth

Performs the HTTP Digest challenge-response handshake automatically.

| Field | Description |
|-------|-------------|
| `username` | Username |
| `password` | Password |

---

## Collections

### What is a collection?

A collection is a directory on your filesystem. requo stores everything in plain text files you can commit to Git alongside your project code.

```
my-api/
├── requo.json          ← collection manifest
├── get-users.req.json  ← a saved request
├── create-user.req.json
└── admin/
    ├── folder.settings.json   ← folder-level defaults
    └── delete-user.req.json
```

### requo.json — the manifest

```json
{
  "schemaVersion": 1,
  "name": "My API",
  "description": "Optional description",
  "variables": [
    { "name": "baseUrl", "value": "https://api.example.com" },
    { "name": "apiKey", "value": "", "secret": true }
  ],
  "environments": [
    {
      "name": "Production",
      "variables": [
        { "name": "baseUrl", "value": "https://api.example.com" }
      ]
    },
    {
      "name": "Staging",
      "variables": [
        { "name": "baseUrl", "value": "https://staging.api.example.com" }
      ]
    }
  ],
  "activeEnvironment": "Production"
}
```

### Request files — `.req.json`

Each saved request is one JSON file. Small, readable diffs in Git.

```json
{
  "name": "Get user",
  "method": "GET",
  "url": "{{baseUrl}}/users/{{userId}}",
  "headers": [
    { "key": "Accept", "value": "application/json", "enabled": true }
  ],
  "query": [],
  "auth": { "type": "bearer", "token": "{{apiKey}}" },
  "preRequest": "",
  "postResponse": "requo.test('is 200', () => requo.expect(requo.response.status).toBe(200))",
  "extractions": [
    { "name": "userId", "type": "jsonpath", "expr": "$.id" }
  ],
  "timeoutMs": 0,
  "docs": "Fetches a single user by ID."
}
```

### Folder settings — `folder.settings.json`

Apply defaults to every request in a folder (and its subfolders). Children override with their own values.

```json
{
  "headers": [
    { "key": "X-Api-Version", "value": "2", "enabled": true }
  ],
  "auth": { "type": "bearer", "token": "{{adminToken}}" },
  "preRequest": "console.log('folder pre-script')",
  "postResponse": ""
}
```

Inheritance rules:
- **Headers**: parent headers are added first, then child headers; if the same key appears in both, the child value wins.
- **Auth**: child overrides parent completely.
- **Scripts**: parent script runs before the child's own script.

### Bruno collection support

Open any existing Bruno project folder directly in requo. All `.bru` files are read and written in-place — no conversion, no migration. The collection shows a **Bruno** badge in the sidebar.

Supported Bruno body types: `json`, `text`, `xml`, `graphql`, `form-urlencoded`, `multipart-form`, `file`, `sparql`.

Supported Bruno auth types: `bearer`, `basic`, `apikey`, `awsv4`, `digest`, `oauth2`.

### OpenAPI support

Import any OpenAPI 3.0 (or Swagger 2.0) spec as a collection. requo generates one request per operation, groups them by tag into folders, and maps parameters, request bodies, and security schemes to the appropriate fields.

You can also export a native collection back to an OpenAPI 3.0 JSON spec.

---

## Environments & variables

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/environments.png" alt="Environment manager" width="700">

### Defining variables

Variables are defined in `requo.json` at the collection level (collection variables) or inside an environment block (environment variables). Secret variables are not written to disk — they are stored in the OS keychain.

```json
{
  "variables": [
    { "name": "baseUrl",  "value": "https://api.example.com" },
    { "name": "apiKey",   "value": "",   "secret": true   },
    { "name": "userId",   "value": "",   "prompt": true, "label": "Enter a user ID" }
  ]
}
```

| Flag | Behaviour |
|------|-----------|
| `secret: true` | Value stored in OS keychain, never written to collection files |
| `prompt: true` | A dialog asks for the value each time the request is sent |

### Using variables

Wrap the variable name in `{{ }}` in any field — URL, header name or value, body, auth:

```
https://{{baseUrl}}/users/{{userId}}
Authorization: Bearer {{apiKey}}
```

### Resolution order

When a `{{name}}` token is resolved, requo searches in this order (first match wins):

1. **Runtime variables** — set by `requo.setVar()` in a script, or extracted from a previous response
2. **Environment variables** — the active environment's variable block
3. **Collection variables** — the top-level `variables` array in `requo.json`
4. **Dynamic variables** — built-in `{{$randomInt}}`, `{{$timestamp}}`, etc. (see next section)

### Switching environments

Use the environment selector in the top bar to switch instantly. The selected environment name is saved in `requo.json` as `activeEnvironment`.

---

## Dynamic variables

Dynamic variables are generated fresh each time a request is sent. Reference them exactly like regular variables — `{{$name}}`.

### Identifiers

| Variable | Output example | Description |
|----------|---------------|-------------|
| `{{$guid}}` | `"a4e5e1c2-..."` | Random RFC 4122 v4 UUID |
| `{{$randomUUID}}` | `"a4e5e1c2-..."` | Alias for `$guid` |
| `{{$randomInt}}` | `"741"` | Random integer in [0, 1000] |
| `{{$randomBoolean}}` | `"true"` | Random boolean |
| `{{$randomAlphaNumeric}}` | `"xK3mPq7r"` | 8-character alphanumeric string |
| `{{$randomPassword}}` | `"aB3$kLm!9pQr"` | 12-character mix of letters, digits, symbols |

### Names & people

| Variable | Output example |
|----------|---------------|
| `{{$randomFirstName}}` | `"Alice"` |
| `{{$randomLastName}}` | `"Smith"` |
| `{{$randomFullName}}` | `"Alice Smith"` |
| `{{$randomUserName}}` | `"alice412"` |
| `{{$randomEmail}}` | `"alice@example.com"` |
| `{{$randomJobTitle}}` | `"Senior Developer"` |
| `{{$randomPhoneNumber}}` | `"+1-555-382-9104"` |

### Time

| Variable | Output example | Description |
|----------|---------------|-------------|
| `{{$timestamp}}` | `"1719514800"` | Unix epoch in seconds |
| `{{$isoTimestamp}}` | `"2024-06-27T13:00:00.000Z"` | RFC 3339 with milliseconds |

### Network & web

| Variable | Output example |
|----------|---------------|
| `{{$randomUrl}}` | `"https://api.example.com/v1/resources/42"` |
| `{{$randomDomainName}}` | `"alice.example.com"` |
| `{{$randomIP}}` | `"192.168.4.17"` |
| `{{$randomIPV6}}` | `"2001:0db8:85a3::8a2e"` |
| `{{$randomMACAddress}}` | `"1a:2b:3c:4d:5e:6f"` |
| `{{$randomHexColor}}` | `"#a3f0c2"` |

### Location

| Variable | Output example |
|----------|---------------|
| `{{$randomLatitude}}` | `"48.858844"` |
| `{{$randomLongitude}}` | `"2.294351"` |
| `{{$randomCity}}` | `"Tokyo"` |
| `{{$randomCountry}}` | `"Germany"` |

### Text

| Variable | Output example |
|----------|---------------|
| `{{$randomLoremWord}}` | `"ipsum"` |
| `{{$randomWord}}` | `"lorem"` |
| `{{$randomFloat}}` | `"0.7312"` |

---

## Scripts

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/scripts.png" alt="Scripts tab" width="800">

Scripts run in a sandboxed JavaScript engine with no filesystem or network access. TypeScript is automatically transpiled.

### When scripts run

```
Request sent
  ↓
Pre-request script    ← runs first; can modify vars, skip the request
  ↓
Variable resolution   ← {{vars}} replaced with current values
  ↓
Auth applied
  ↓
HTTP request executed
  ↓
Post-response script  ← can read response, run tests, extract vars
  ↓
Variable extractions  ← declarative JSON/header/regex captures
  ↓
Results displayed
```

### The `requo` object

The entire scripting API lives on the global `requo` object.

#### Variables

```js
requo.getVar('name')           // → string (current value or "")
requo.setVar('name', 'value')  // sets a runtime variable for this run
```

#### Assertions

```js
requo.test('status is 200', function () {
  requo.expect(requo.response.status).toBe(200)
})

requo.test('body has id', function () {
  const body = requo.response.json()
  requo.expect(body.id).toBeTruthy()
})
```

**Assertion methods on `requo.expect(actual)`:**

| Method | Description |
|--------|-------------|
| `.toBe(expected)` | Strict equality (`===`) |
| `.toEqual(expected)` | Deep equality (JSON-stringified comparison) |
| `.toContain(substring)` | String contains |
| `.toBeLessThan(n)` | Numeric `<` |
| `.toBeGreaterThan(n)` | Numeric `>` |
| `.toBeTruthy()` | Value is truthy |

#### The `requo.response` object (post-response only)

| Property | Type | Description |
|----------|------|-------------|
| `.status` | `number` | HTTP status code |
| `.headers` | `object` | Response headers (lowercase keys) |
| `.body` | `string` | Raw response body string |
| `.json()` | `any` | Parses body as JSON; returns `undefined` on error |

#### The `requo.request` object (pre-request only)

| Property | Type | Description |
|----------|------|-------------|
| `.method` | `string` | HTTP method |
| `.url` | `string` | Request URL (before variable resolution) |
| `.body` | `string` | Request body string |

#### Flow control (`requo.runner`)

```js
// Skip this request without marking it as failed
requo.runner.skipRequest()

// Stop the entire collection run immediately
requo.runner.stopExecution()

// Jump to a named request (enables request chaining in a runner)
requo.runner.setNextRequest('Create User')
```

#### Sending sub-requests (`requo.sendRequest`)

Chain HTTP calls inside a script (max depth: 5):

```js
const res = requo.sendRequest({
  method: 'GET',
  url: 'https://api.example.com/token',
  headers: [{ key: 'Accept', value: 'application/json' }],
})
requo.setVar('token', res.json().access_token)
```

The `res` object has the same shape as `requo.response`.

#### Logging

```js
console.log('status:', requo.response.status)
```

Logs appear in the **Console** tab of the response panel after the request runs.

#### CommonJS modules

Scripts can `require()` relative `.js` or `.ts` files from the collection root:

```js
// helpers.js in the collection root
module.exports = { toBase64: (s) => btoa(s) }

// In a script:
const { toBase64 } = require('./helpers')
```

Paths traversing outside the collection root (`../`) are rejected for security.

---

## Variable extraction

Capture values from a response automatically and pass them to later requests in a run. Define extractions in the **Extract** tab — no scripting required.

| Type | Expression syntax | Example |
|------|-----------------|---------|
| **JSONPath** | `$.field`, `$.a.b[0]`, `$..field` | `$.data.id` |
| **Header** | Header name (case-insensitive) | `Location` |
| **Regex** | Go RE2 regex; first capture group used | `"token":"([^"]+)"` |
| **Status** | *(no expression)* | Returns status code as string |
| **Body** | *(no expression)* | Returns entire response body |

Each extraction has a `default` value used when the expression yields nothing.

Extracted variables flow into all subsequent requests in the same collection run, making it easy to chain: **Create User → Get User → Delete User** using the `id` extracted from the Create response.

---

## Response viewer

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/response-viewer.png" alt="Response viewer" width="800">

### Body tab

- **Pretty**: syntax-highlighted JSON / XML / HTML with fold/unfold. Press **⌘F / Ctrl+F** to search.
- **Raw**: plain text.
- **Preview**: rendered HTML page or image; binary files show a download button.

Copy the entire body with the copy icon, or save it as a file (honours `Content-Disposition: filename`).

### Headers tab

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/response-headers.png" alt="Response headers" width="800">

Filterable table of all response headers. The tab badge shows the header count.

### Tests tab

Shows the outcome of every `requo.test()` call from your post-response script:

```
✓ status is 200
✗ body has id → Expected undefined to be truthy
```

Summary line: **2/3 passed**.

### Console tab

`console.log()` output from your pre- and post-response scripts, in order.

### Timeline tab

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/timeline.png" alt="Request timeline" width="800">

A history of all requests made in this tab. Each entry is expandable and shows:
- **Request** sub-tab: method, URL, headers, body
- **Response** sub-tab: status, headers, body
- **Network Logs**: the raw HTTP exchange formatted for easy reading

### Timing tab

See the [Response timing](#response-timing) section below.

### Code tab

Generate equivalent code for the request:

| Language | Notes |
|----------|-------|
| cURL | Shell command with `-H` and `-d` flags |
| JavaScript | `fetch()` with async/await |
| Python | `requests` library |
| Go | `net/http` standard library |
| HTTP | Raw HTTP/1.1 message |

---

## Response timing

Every response shows a phase-level latency breakdown in the **Timing** tab.

| Phase | Colour | Measures |
|-------|--------|---------|
| **DNS** | Indigo | Domain name resolution (0 if cached or using IP directly) |
| **Connect** | Sky | TCP three-way handshake (0 if connection was reused) |
| **TLS** | Amber | SSL/TLS handshake (HTTPS requests only) |
| **TTFB** | Green | Time from first byte sent to first byte received |
| **Total** | — | Wall-clock round-trip duration |

The stacked bar lets you see at a glance which phase dominates. Below the bar a table shows each phase value in milliseconds.

Over multiple requests in the same tab, the Timing tab also displays a historical chart — latest, average, minimum, and maximum total latency — so you can spot drift without leaving the app.

---

## Collection runner & test suites

<img src="https://github.com/yanislavm/requo-releases/raw/master/screenshots/runner.png" alt="Collection runner" width="800">

### Running requests

Right-click a collection or folder in the sidebar and choose **Run All**. Every request executes in order; pre/post scripts and variable extractions run normally.

### Flow control in a run

From any script:

```js
// Skip the current request (not counted as failed)
requo.runner.skipRequest()

// Stop the run immediately
requo.runner.stopExecution()

// Jump to a different request by name (enables dynamic ordering)
requo.runner.setNextRequest('Refresh Token')
```

> A safety limit of 1 000 steps per run prevents infinite loops.

### Results

The runner panel shows:
- Per-request status, duration, and pass/fail badge
- Individual test outcomes from `requo.test()` calls
- Variable values set by extractions and scripts
- Aggregated totals: total / passed / failed / skipped

### Test suites

A test suite is a named subset of a collection — a curated list of request paths you want to run together (e.g. a smoke test or regression suite). Suites are defined in `requo.json`:

```json
{
  "suites": [
    {
      "name": "Smoke",
      "requests": ["get-health.req.json", "auth/login.req.json"]
    }
  ]
}
```

Run a suite from the collection context menu → **Run Suite** and pick the suite name. Results are identical to a folder run.

---

## gRPC

Call gRPC services directly — no `.proto` files needed. requo uses **server reflection** to discover available services and methods at runtime.

### Setup

1. Open a new tab and select **gRPC** from the method dropdown.
2. Enter the server address (`host:port`).
3. Toggle **TLS** on or off.
4. Click **List services** — requo calls the reflection API and populates the service tree.
5. Select a service and method, fill in the JSON request body, and click **Invoke**.

### Request body

Enter a JSON object that maps to the protobuf message fields. requo marshals it to binary on the wire and unmarshals the response back to JSON automatically.

### Metadata (gRPC headers)

Add gRPC metadata (equivalent to HTTP headers) as key/value pairs in the **Metadata** tab.

### Streaming

| Call type | Support |
|-----------|---------|
| Unary | ✓ Full |
| Server-streaming | ✓ Returns array of messages |
| Client-streaming | — Not supported |
| Bidirectional | — Not supported |

---

## WebSocket

1. Select **WebSocket** from the method dropdown.
2. Enter the `ws://` or `wss://` URL.
3. Add any handshake headers in the Headers tab.
4. Click **Connect**.

Once connected, type a message and press **Send**. Incoming and outgoing messages are listed in the message stream with direction indicators and timestamps. Click **Disconnect** to close the session.

---

## Server-sent events (SSE)

1. Select **SSE** from the method dropdown (or set the method to GET with an SSE endpoint).
2. Add headers, auth, and query parameters as usual.
3. Click **Connect** to open the stream.

Each SSE event appears as it arrives. The event `id`, `event` type, and `data` fields are displayed. Click **Disconnect** to close.

---

## MQTT

Connect to any MQTT broker (v3.1.1 and v5 supported).

| Field | Description |
|-------|-------------|
| **Broker URL** | `mqtt://host:1883`, `mqtts://host:8883`, `tcp://host:1883` |
| **Client ID** | Optional; auto-generated if empty |
| **Username / Password** | Optional broker credentials |
| **TLS** | Enable encrypted connection |

After connecting:
- **Subscribe** to a topic (with QoS 0/1/2) to receive messages.
- **Publish** a payload to a topic (with QoS 0/1/2 and optional retain flag).

Incoming messages and your published messages appear in the message stream with topic, payload, QoS, and timestamp.

---

## Load testing

Stress-test an endpoint directly from requo. Configure the run, watch live metrics, and compare runs over time.

### Configuration

| Field | Description |
|-------|-------------|
| **Target TPS** | Requests per second to aim for (0 = unbounded) |
| **Concurrency** | Number of parallel workers |
| **Ramp-up** | Seconds to linearly increase from 0 to target TPS |
| **Duration** | Run for N seconds (mutually exclusive with Iterations) |
| **Iterations** | Stop after N total requests |
| **Think time** | Millisecond pause each worker waits between requests |

### Live metrics (updated every 250 ms)

| Metric | Description |
|--------|-------------|
| Elapsed | Time since run started |
| Sent | Total requests dispatched |
| Completed | Requests with a response |
| Errors | Requests that failed (connection refused, timeout, etc.) |
| Current TPS | Windowed throughput (requests/sec) |
| P50 / P95 / P99 | Latency percentiles in milliseconds |
| Min / Max / Mean | Latency extremes |
| Error rate | `errors / completed` |

### Run history

Completed runs are saved to disk and listed in the **Load Test** panel. Select any run to replay its metrics chart.

---

## Mock server

Spin up a local HTTP mock that returns the body and headers defined in your collection's requests.

### Starting a mock

Right-click a collection → **Start mock server** and optionally specify a port (leave 0 for a random available port). The mock binds to `127.0.0.1` only.

### Route matching

Each saved request becomes a route: `METHOD /path`. Path parameters (`:id` or `{id}`) match any single segment. When a real request arrives, the mock finds the best matching route and returns:

- **Status**: 200 (default)
- **Headers**: from the request's Content-Type / response headers
- **Body**: the request body with `{{name}}` placeholders replaced by `<name>` markers

### Request log

Every incoming request is logged with timestamp, method, path, status code, and whether a route matched. View the log in the **Mock** panel.

---

## AI assistant

requo includes an AI assistant that can see the current request and response and answer questions about them.

### Setup

Open **Settings → AI**, choose a provider, enter your API key, and optionally override the model name.

| Provider | Default model | Notes |
|----------|---------------|-------|
| **Anthropic** | `claude-sonnet-4-6` | |
| **OpenAI** | `gpt-4o-mini` | |
| **Google Gemini** | `gemini-2.0-flash` | |
| **Mistral** | `mistral-small-latest` | |
| **Groq** | `llama-3.3-70b-versatile` | |
| **xAI (Grok)** | `grok-3-mini` | |
| **DeepSeek** | `deepseek-chat` | |
| **Perplexity** | `sonar` | |
| **Ollama** | `llama3` | Local; no API key needed; set base URL to your Ollama instance |
| **OpenAI-compatible** | *(custom)* | Set base URL to any OpenAI-compatible endpoint |

API keys are stored in the OS keychain — never written to any file. The model field is free-text so you can use any model name the provider supports.

### What you can ask

- Explain a response body
- Identify why a request failed
- Generate test assertions
- Suggest request body values from a schema
- Draft a pre/post-request script
- Anything else you'd ask an AI with full context of the HTTP exchange

---

## Secret vaults

Reference secrets from an external vault directly in any request field. Secrets are resolved at send time and never stored in your collection files.

### Syntax

```
{{vault:alias/path#field}}
```

| Part | Description |
|------|-------------|
| `alias` | The vault alias configured in **Settings → Vaults** |
| `path` | Secret path (vault-specific) |
| `field` | Optional field within a JSON secret |

**Examples:**

```
# HashiCorp Vault — KV v2, field "password" from secret at path "database/prod"
Authorization: Bearer {{vault:prod/database/prod#password}}

# AWS Secrets Manager — key "api_key" from secret "myapp/keys"
X-Api-Key: {{vault:aws/myapp/keys#api_key}}

# Azure Key Vault — secret named "stripe-key"
Authorization: Bearer {{vault:azure/stripe-key}}
```

### Supported providers

#### HashiCorp Vault

Reads secrets via the Vault HTTP API.

| Setting | Description |
|---------|-------------|
| **Alias** | Identifier used in `{{vault:alias/...}}` |
| **Address** | Vault server URL (e.g. `https://vault.example.com`) |
| **Token** | Vault token (stored in OS keychain) |

Supports KV v1 (`data.{field}`) and KV v2 (`data.data.{field}`) secret engines.

#### AWS Secrets Manager

Calls the Secrets Manager API using AWS Signature Version 4.

| Setting | Description |
|---------|-------------|
| **Alias** | Identifier used in `{{vault:alias/...}}` |
| **Address** | AWS region (e.g. `us-east-1`) |
| **Access Key ID** | AWS access key (stored in OS keychain) |
| **Secret Access Key** | AWS secret access key (stored in OS keychain) |

#### Azure Key Vault

Authenticates via OAuth2 client credentials and reads secrets from the Azure Key Vault REST API.

| Setting | Description |
|---------|-------------|
| **Alias** | Identifier used in `{{vault:alias/...}}` |
| **Address** | Key Vault name (e.g. `my-vault`) — HTTPS URL is constructed automatically |
| **Tenant ID** | Azure AD tenant ID |
| **Client ID** | App registration client ID |
| **Client Secret** | App registration client secret (stored in OS keychain) |

### Caching

Resolved secrets are cached in memory for 5 minutes. The cache is local to the running requo process and is never persisted to disk.

---

## Import & export

### Import

| Format | How to import |
|--------|--------------|
| **Postman v2.1** | File → Import → Postman → select the `.json` export |
| **Insomnia v4** | File → Import → Insomnia → select the export file |
| **Bruno** | File → Open folder → point to your Bruno collection directory |
| **OpenAPI 3.0 / Swagger** | File → Import → OpenAPI → select `.json` or `.yaml` |
| **HAR** | File → Import → HAR → select the `.har` file |
| **cURL** | Edit menu → Paste as cURL, or type/paste in the URL bar with `curl` prefix |
| **URL** | Type or paste any `http://` / `https://` URL directly in the URL bar |

### Export

| Format | How to export |
|--------|--------------|
| **OpenAPI 3.0** | Right-click collection → Export → OpenAPI |
| **Postman** | Right-click collection → Export → Postman |
| **Insomnia** | Right-click collection → Export → Insomnia |

---

## Git integration

When a collection folder is a Git repository, the **Git** panel (available from the collection context menu) gives you basic version control without leaving requo.

| Operation | Description |
|-----------|-------------|
| **Status** | Shows modified, staged, and untracked files |
| **Diff** | Line-by-line diff for any changed file |
| **Branches** | Lists all local branches with ahead/behind tracking counts |
| **Checkout** | Switch to an existing branch |
| **New branch** | Create and switch to a new branch |
| **Stash** | Stash uncommitted changes |
| **Pop stash** | Apply the most recent stash |
| **Pull** | Fetch and merge from the tracked remote |
| **Push** | Push the current branch to the tracked remote |

> Commit and merge operations are intentionally left to your normal Git workflow — requo's Git panel is for quick navigation, not a full Git UI.

---

## Cookie jar

requo maintains a per-session cookie jar. Cookies received in `Set-Cookie` response headers are stored and sent automatically on subsequent requests to the matching domain and path — exactly as a browser would.

- View all stored cookies in **Settings → Cookies**.
- Delete individual cookies or clear the entire jar.
- Disable cookie handling globally in **Settings → Network → Enable cookie jar**.
- Disable cookie handling for a single request with the **Disable cookies** toggle in the request's settings panel.

---

## History

Every request you send is recorded in the request **Timeline** tab. The history is also accessible from the **History** view (icon in the left sidebar).

| Stored field | Description |
|-------------|-------------|
| Timestamp | When the request was sent |
| Method + URL | What was sent |
| Status | HTTP status code |
| Duration | Round-trip time |
| Request details | Method, URL, headers, body |
| Response details | Status, headers, body |

**Limits**: The most recent N entries are kept (default 100, configurable in **Settings → History → Max history**). Oldest entries are evicted automatically.

**Export to HAR**: Export the full history as an HTTP Archive (`.har`) file for use in other tools.

---

## Settings reference

Open **Settings** from the gear icon in the top bar.

### Appearance

| Setting | Default | Description |
|---------|---------|-------------|
| **Theme** | System | `system` (follows OS), `light`, or `dark` |
| **Font size** | 13 | Editor font size in pixels |
| **Wrap long lines** | On | Wrap or scroll long lines in editors |

### Network

| Setting | Default | Description |
|---------|---------|-------------|
| **Timeout** | 30 000 ms | Request timeout in milliseconds (0 = no limit) |
| **Follow redirects** | On | Follow HTTP 3xx redirects automatically |
| **Enable cookie jar** | On | Send and store cookies |

### Proxy & TLS

| Setting | Default | Description |
|---------|---------|-------------|
| **Proxy URL** | — | `http://`, `https://`, or `socks5://` proxy (empty = system proxy) |
| **Verify TLS certificates** | On | Disable to accept self-signed or expired certificates |
| **Client certificate** | — | Path to a PEM client certificate for mutual TLS |
| **Client key** | — | Path to the PEM private key for the client certificate |

### History

| Setting | Default | Description |
|---------|---------|-------------|
| **Max history** | 100 | Number of past requests to keep |

### AI

| Setting | Default | Description |
|---------|---------|-------------|
| **Provider** | — | AI provider (see [AI assistant](#ai-assistant)) |
| **Model** | — | Model name (empty = provider default) |
| **Base URL** | — | Custom endpoint for Ollama or OpenAI-compatible servers |
| **API key** | — | Stored in OS keychain; set per-provider |

### Vaults

Manage external secret vault aliases. Each alias has:

| Field | Description |
|-------|-------------|
| **Alias** | Short name used in `{{vault:alias/...}}` |
| **Type** | `hashicorp`, `aws`, or `azure` |
| **Address** | Vault URL / AWS region / Azure vault name |
| **Tenant ID** | Azure only |
| **Client ID** | Azure only |

Credentials (tokens, secret keys, client secrets) are stored in the OS keychain — click **Set credential** next to each vault to enter them. Use **Test connection** to verify the vault is reachable with the stored credential.

### License

Activate a requo Pro or Team license key. Free tier does not require a key. Licenses are validated offline using an embedded Ed25519 public key — no internet connection required for validation.

---

## Keyboard shortcuts

| Shortcut | Action |
|----------|--------|
| **⌘↵** / **Ctrl+Enter** | Send request |
| **⌘T** / **Ctrl+T** | New request tab |
| **⌘S** / **Ctrl+S** | Save request to collection |
| **⌘K** / **Ctrl+K** | Open command palette |
| **⌘F** / **Ctrl+F** | Search in response body |
| **?** | Show keyboard shortcuts reference |
| **Esc** | Close dialog, palette, or menu |

---

## Server mode (headless)

`requo-server` is a standalone HTTP API server that exposes the same core engine without the desktop UI. It is useful for CI pipelines, browser-based integrations, or custom tooling.

### Starting the server

```bash
# Generate a random token automatically (printed to stdout)
requo-server

# Use a specific token and bind to a custom address
REQUO_API_TOKEN=my-secret requo-server -addr 127.0.0.1:34115

# Allow a browser frontend to connect (CORS)
requo-server -origins http://localhost:5173
```

| Flag / env var | Default | Description |
|----------------|---------|-------------|
| `-addr` | `127.0.0.1:34115` | Listen address (loopback only) |
| `-origins` | — | Comma-separated CORS origins |
| `REQUO_API_TOKEN` | *(random)* | Bearer token all clients must present |
| `REQUO_NO_SESSION` | — | Set to `1` to disable session persistence (in-memory only) |

### Authentication

Every request (except `GET /api/health`) must carry:

```
Authorization: Bearer <token>
```

### Core endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/health` | Health check — no auth required |
| `POST` | `/api/execute` | Execute a request |
| `GET` | `/api/history` | List request history |
| `DELETE` | `/api/history` | Clear history |
| `GET` | `/api/history/export/har` | Export history as HAR |
| `GET` | `/api/settings` | Get settings |
| `PATCH` | `/api/settings` | Partial-update settings |
| `POST` | `/api/settings/reset` | Reset to defaults |

### Collection endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/collections` | List open collections |
| `POST` | `/api/collections/open` | Open a collection by filesystem path |
| `POST` | `/api/collections/create` | Create a new collection |
| `POST` | `/api/collections/close` | Close a collection (files unchanged) |
| `GET` | `/api/collections/{id}` | Get a collection with its full tree |
| `POST` | `/api/collections/{id}/requests` | Add a new request |
| `PUT` | `/api/collections/{id}/requests` | Save an existing request |
| `DELETE` | `/api/collections/{id}/requests` | Delete a request |
| `POST` | `/api/collections/{id}/run` | Run all requests in the collection |
| `POST` | `/api/collections/{id}/run-suite` | Run a named suite |

### Cookie endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/cookies` | List all stored cookies |
| `POST` | `/api/cookies` | Set a cookie |
| `DELETE` | `/api/cookies` | Delete a cookie |
| `DELETE` | `/api/cookies/all` | Clear all cookies |

### Session persistence

With `REQUO_NO_SESSION` unset, the server saves the list of open collection paths to `~/.config/requo/session.json` so they are restored on restart. Settings are saved to `~/.config/requo/settings.json`.

Set `REQUO_NO_SESSION=1` to disable persistence (useful in CI or ephemeral environments).

---

## Source code

requo is open source. The source repository is at **[yanislavm/requo](https://github.com/yanislavm/requo)**.

---

## License

MIT © Yanislav Mihaylov
