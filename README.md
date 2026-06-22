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

  <img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/main.png" alt="requo main window" width="900">
</div>

---

## Why requo?

| | requo | Postman | Insomnia |
|---|---|---|---|
| **Stores data** | Plain files on disk | Postman cloud | Local + cloud |
| **Git-friendly** | Yes — commit collections alongside code | No | Partial |
| **No account needed** | Yes | No | No |
| **Open source** | Yes | No | Partial |
| **Offline-first** | Yes | Limited | Yes |

---

## Features

### HTTP Requests
Send any HTTP request with full control over method, URL, query parameters, headers, and body. JSON body editor with syntax highlighting and auto-prettify.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/request-editor.png" alt="Request editor" width="800">

### Response Viewer
Syntax-highlighted response with status, timing, and size at a glance. Switch between Pretty, Raw, Headers, and Preview (HTML) tabs. Search within the response body with Ctrl+F.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/response-viewer.png" alt="Response viewer" width="800">

### Collections
Organise requests into folders. Collections are stored as plain files — commit them to your repo and share with your team without any sync account.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/collections.png" alt="Collections sidebar" width="400">

### Environments & Variables
Define environments (dev, staging, prod) with named variables. Switch environments instantly without editing requests. Export environments to JSON for sharing.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/environments.png" alt="Environment manager" width="700">

### Authentication
Built-in support for Bearer token, Basic auth, API key, and OAuth 2.0 (Client Credentials and Resource Owner Password grant types).

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/auth-editor.png" alt="Auth editor" width="700">

### Request Timeline
Every request in a tab is recorded in a timeline. Inspect past requests, compare responses, and see exactly what was sent — including variables resolved at runtime.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/timeline.png" alt="Request timeline" width="800">

### Collection Runner & Test Suites
Run an entire collection or folder in sequence. Write pre/post-request scripts and assertions to build automated test suites. View aggregated pass/fail results.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/runner.png" alt="Collection runner" width="800">

### Git Integration
Commit your collection directly from the app. View git status and diffs without leaving requo.

<img src="https://github.com/yanislavm/requo-releases/raw/main/screenshots/git-commit.png" alt="Git commit dialog" width="600">

### Import & Export
Import from **Postman v2.1**, **Insomnia v4**, **Bruno**, **OpenAPI**, **HAR**, and URL. Export to **Postman**, **OpenAPI 3.0**, and **Insomnia** formats.

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

### CLI (all platforms)

The `requo` CLI and `requo-server` (headless API server) are available as standalone binaries for all platforms. Download from [Releases](https://github.com/yanislavm/requo-releases/releases/latest).

```bash
# Run a collection from the command line
requo run ./my-collection --env production
```

---

## Quick start

1. **Open requo** and click **+** → **New Collection**
2. Add a request — enter a URL and hit **Send**
3. Save the request to your collection
4. Create an **Environment** with a `baseUrl` variable, reference it as `{{baseUrl}}` in your requests
5. Commit your collection with **Git Commit…** from the collection context menu

---

## Verify a download

SHA-256 checksums are published alongside each release in `checksums.txt`.

```bash
# macOS / Linux
shasum -a 256 -c checksums.txt

# Windows (PowerShell)
Get-FileHash requo-*-windows-amd64.zip -Algorithm SHA256
```

---

## Source code

requo is open source. The source repository is at **[yanislavm/requo](https://github.com/yanislavm/requo)**.

---

## License

MIT © Yanislav Mihaylov
