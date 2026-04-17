# CazeAppSecAI — User Guide
## DAST & SCA Modules

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Navigation Basics](#2-navigation-basics)
3. [DAST — Settings & Configuration](#3-dast--settings--configuration)
   - 3.1 [Configure DAST Target URLs](#31-configure-dast-target-urls)
   - 3.2 [Configure ZAP Scanner Settings](#32-configure-zap-scanner-settings)
   - 3.3 [Configure ZAP Report Path (Upload)](#33-configure-zap-report-path-upload)
   - 3.4 [Configure Deployment & Environment Context](#34-configure-deployment--environment-context)
   - 3.5 [Test DAST Connections](#35-test-dast-connections)
4. [SCA — Settings & Configuration](#4-sca--settings--configuration)
   - 4.1 [Configure SCA (Trivy) Scan Settings](#41-configure-sca-trivy-scan-settings)
   - 4.2 [Configure SCA Context Modifiers](#42-configure-sca-context-modifiers)
5. [Saving Your Configuration](#5-saving-your-configuration)
6. [Running a DAST Scan](#6-running-a-dast-scan)
7. [Running an SCA Scan](#7-running-an-sca-scan)
8. [Quick Reference — Navigation Commands](#8-quick-reference--navigation-commands)

---

## 1. Getting Started

Launch the AppSecAI executable. On first run you will see:

```
╔══════════════════════════════════════════════════════════════╗
║                      CazeAppSecAI CLI                        ║
╚══════════════════════════════════════════════════════════════╝

🔵 Essential configuration missing:
  • SAST: Configurations
  • DAST: Configurations
  • SCA:  Configurations

Would you like to run the Setup Wizard now? (Y/n):
```

- Enter `Y` to run the guided setup wizard (recommended for first-time users).
- Enter `n` to skip and configure manually from the Settings menu.

After the wizard (or skipping it), press **Enter** to reach the **Main Menu**.

```
┌─────────────────────────────────────────────────────────────┐
│                        MAIN MENU                            │
├─────────────────────────────────────────────────────────────┤
│  1.  Settings & Configuration                               │
│  2.  Security Analysis                                      │
│  3.  How To                                                 │
│  4.  Help                                                   │
│  5.  About Us                                               │
│  0.  Exit                                                   │
└─────────────────────────────────────────────────────────────┘
```

The breadcrumb at the top of each screen shows your current location, for example:
```
C:\...\CazeAppSecReport> Main Menu > Settings Menu > DAST Configuration
```

---

## 2. Navigation Basics

The CLI supports both number-based and `cd`-style navigation.

| Command | Action |
|---|---|
| `1`, `2`, `3` … | Select a numbered menu option |
| `cd ..` | Go back one level |
| `cd /` | Jump to Main Menu from anywhere |
| `cd dast` | Jump directly to DAST Configuration |
| `cd sca` | Jump directly to SCA Configuration |
| `cd settings` | Jump to Settings Menu |
| `cd scan` | Jump to Security Analysis |
| `cd/list` | Show all available navigation shortcuts |

You can also use `cd <menu name>` with partial names — the CLI will fuzzy-match the closest option.

---

## 3. DAST — Settings & Configuration

**Path:** Main Menu → `1` Settings & Configuration → `2` DAST Configurations

```
┌─────────────────────────────────────────────────────────────┐
│               [DAST] DYNAMIC ANALYSIS SETTINGS              │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure DAST Target URL(s)                           │
│  2.  Configure ZAP Scanner Settings                         │
│  3.  Configure ZAP Report Path (Upload)                     │
│  4.  Configure Deployment & Environment Context             │
│  5.  Test DAST Connections (Target URLs)                    │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘
```

---

### 3.1 Configure DAST Target URL(s)

Select option `1` from the DAST Dynamic Analysis Settings menu.

```
📍 Configure DAST Target URL(s)

Options:
1. Set single URL
2. Set multiple URLs (one by one)
3. Set multiple URLs (comma-separated)
4. View current URLs
5. Clear all URLs
```

**Option 1 — Single URL**

Enter the full URL of the web application you want to scan:
```
Enter DAST target URL (e.g., http://localhost:8080): http://myapp.example.com
✅ DAST URL set to: http://myapp.example.com
```

**Option 2 — Multiple URLs (one by one)**

Enter each URL on a separate line. Press **Enter** on an empty line to finish:
```
URL 1: http://app1.example.com
   ✅ Added: http://app1.example.com
URL 2: http://app2.example.com
   ✅ Added: http://app2.example.com
URL 3:          ← press Enter to stop
✅ Configured 2 DAST URLs
```

**Option 3 — Multiple URLs (comma-separated)**

Paste all URLs in one line, separated by commas:
```
Enter URLs separated by commas: http://app1.example.com, http://app2.example.com
✅ Configured 2 DAST URLs
```

**Option 4 — View current URLs**

Displays all currently configured target URLs.

**Option 5 — Clear all URLs**

Removes all configured URLs after a confirmation prompt.

> **Tip:** The first URL in a multi-URL list becomes the primary `DAST_URL` used in the settings summary.

---

### 3.2 Configure ZAP Scanner Settings

Select option `2` from the DAST Dynamic Analysis Settings menu.

This option opens the `app_config.yaml` file for advanced ZAP configuration. The key settings you can modify there are:

| Setting | Default | Description |
|---|---|---|
| ZAP installation path | `external/ZAP_2.16.1/` | Path to the bundled ZAP installation |
| Scan policy | `Default Policy` | ZAP active scan policy to apply |
| Max scan time | `3600` seconds | Maximum time allowed for a single scan |
| Spider max depth | `5` | How deep the ZAP spider crawls |

After editing `app_config.yaml`, press **Enter** to return to the DAST menu. Changes take effect on the next scan.

---

### 3.3 Configure ZAP Report Path (Upload)

Select option `3` from the DAST Dynamic Analysis Settings menu.

Use this when you already have a ZAP scan report (from a previous or external scan) and want AppSecAI to analyze it without running a new live scan.

```
📤 Configure ZAP Report Path (Upload for Analysis)
Current Path: (not set)

Enter path to ZAP security_recommendations.json (or '0' to back):
```

1. Paste the full file path to your ZAP `security_recommendations.json` file.
2. The tool validates the path exists before saving.
3. On success:
   ```
   ✅ ZAP report path set and synchronized: C:\path\to\security_recommendations.json
   ```

The uploaded report will be detected automatically when you run a DAST scan — you will be offered the option to analyze it instead of running a fresh live scan.

> **Note:** Paths with spaces should be entered without surrounding quotes; the tool strips them automatically.

---

### 3.4 Configure Deployment & Environment Context

Select option `4` from the DAST Dynamic Analysis Settings menu.

This opens the **Deployment Settings** menu, which lets you describe your application's environment so the AI can produce more accurate, context-aware findings.

```
┌─────────────────────────────────────────────────────────────┐
│              CONFIGURE DEPLOYMENT SETTINGS                  │
├─────────────────────────────────────────────────────────────┤
│  1.  Product and Version                                    │
│  2.  Environment (staging, production, etc.)                │
│  3.  Runtime (container, monitoring)                        │
│  4.  Service (auth, rate limiting)                          │
│  5.  Security Controls (RBAC, WAF, MFA)                     │
│  0.  Back                                                   │
└─────────────────────────────────────────────────────────────┘
```

Fill in as many fields as are relevant to your deployment. These values are stored in `context_modifiers/enhanced_compliance.json` and are used to enrich AI analysis during scans.

---

### 3.5 Test DAST Connections

Select option `5` from the DAST Dynamic Analysis Settings menu.

This performs a live connectivity check against all configured DAST target URLs before you run a scan.

```
🔍 Testing DAST Connections...
========================================

🌐 Testing Target: http://myapp.example.com
   ✅ Status: Reachable (HTTP 200)

🛡️  ZAP Status Check:
   ℹ️  ZAP Status: Ready for new scan
```

Possible results:

| Result | Meaning |
|---|---|
| `✅ Reachable (HTTP 2xx/3xx)` | Target is accessible, scan can proceed |
| `⚠️ HTTP 4xx` | Target responded but returned an error — scan may still work |
| `❌ Connection failed` | Target is unreachable — check URL, network, and firewall |

---

## 4. SCA — Settings & Configuration

**Path:** Main Menu → `1` Settings & Configuration → `3` SCA Configurations

```
┌─────────────────────────────────────────────────────────────┐
│             [SCA] SOFTWARE COMPOSITION SETTINGS             │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure SCA (Trivy) Scan Settings                    │
│  2.  Configure SCA Context Modifiers                        │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘
```

---

### 4.1 Configure SCA (Trivy) Scan Settings

Select option `1` from the SCA Software Composition Settings menu.

This sets the **target type** and **target path** that Trivy will scan.

```
📦 Configure SCA (Trivy) Scan Settings
Current Target Type: fs
Current Target Path: ./

Enter target type (fs, image, repo, k8s, container, vm, rootfs) [fs]:
Enter target path/name (e.g., ./ , python:3.9 , cluster , <container_id>) [./]:
```

**Target Types and Examples**

| Target Type | What it scans | Example value |
|---|---|---|
| `fs` | Local filesystem directory | `./` or `C:\myproject` |
| `repo` | Remote Git repository | `https://github.com/owner/repo` |
| `image` | Container image | `python:3.9` or `ubuntu:22.04` |
| `k8s` | Kubernetes cluster | `cluster` |
| `container` | Running container | `<container_id>` |
| `vm` | Virtual machine image | path to VM image |
| `rootfs` | Root filesystem | path to rootfs |

After entering both values:
```
✅ Trivy target configuration updated to type 'repo', target 'https://github.com/owner/myapp'
```

> **Tip:** If you have a GitHub repository already configured under SAST settings, the SCA scan will detect it and offer to reuse it automatically.

---

### 4.2 Configure SCA Context Modifiers

Select option `2` from the SCA Software Composition Settings menu.

Context modifiers tell the AI engine how your project manages dependencies, so it can prioritize vulnerabilities more accurately.

```
┌─────────────────────────────────────────────────────────────┐
│              CONFIGURE SCA CONTEXT SETTINGS                 │
├─────────────────────────────────────────────────────────────┤
│  1.  dependency management  – Update frequency, SBOM, lock files  │
│  2.  package sources        – Registry, signature verification    │
│  3.  vulnerability response – Patching, monitoring, emergency     │
│  4.  build pipeline         – SLSA, hash verification             │
│  5.  runtime behavior       – Sandboxing, isolation, network      │
│  6.  ecosystem              – Language version, package manager   │
│  7.  compliance             – Standards (SOC2, HIPAA, PCI-DSS)    │
│  0.  back or cd/..          – Return to Settings Menu             │
└─────────────────────────────────────────────────────────────┘
```

Each sub-option presents a set of fields you can fill in. You can also navigate directly using `cd` shortcuts, for example `cd dependency management` or `cd compliance`.

**Sub-option descriptions:**

**1. Dependency Management**
Configure how often dependencies are updated, whether you use lock files, and if you generate an SBOM (Software Bill of Materials).

**2. Package Sources**
Specify which registries your packages come from (e.g., PyPI, npm, Maven) and whether signature verification is enforced.

**3. Vulnerability Response**
Define your patching cadence, monitoring tools in use, and emergency response process for critical CVEs.

**4. Build Pipeline**
Describe your CI/CD pipeline security posture — SLSA level, hash verification, and build reproducibility.

**5. Runtime Behavior**
Indicate whether your application runs in a sandboxed environment, what network access it has, and isolation mechanisms.

**6. Ecosystem**
Set the primary language version and package manager (e.g., Python 3.11 with pip, Node 20 with npm).

**7. Compliance**
Select applicable compliance standards (SOC2, HIPAA, PCI-DSS, etc.) so findings are mapped to relevant controls.

All context settings are stored in `context_modifiers/enhanced_compliance.json`.

---

## 5. Saving Your Configuration

After configuring DAST and/or SCA settings, always save before running a scan so your settings persist across sessions.

**Path:** Main Menu → `1` Settings & Configuration → `5` Save Configuration to .env

```
💾 Saving Configuration...
✅ Configuration saved to .env file
```

The `.env` file is saved in the `CazeAppSecReport` directory and is loaded automatically on the next launch. It stores all settings including DAST URLs, Trivy target, ZAP report path, and context modifiers.



---

## 6. Running a DAST Scan

**Path:** Main Menu → `2` Security Analysis → `2` DAST Analysis (Dynamic Analysis)

### Step 1 — Scan Review

Before the scan starts, AppSecAI shows a summary of your current configuration:

```
══════════════════════════════════════════════════════════════
🔍 SCAN REVIEW: DAST Security Scan
══════════════════════════════════════════════════════════════
  • Repository:  https://github.com/owner/myapp
  • DAST Target: http://myapp.example.com (ZAP)

⚙️  Active Security Frameworks: Default Context
   (To change these, go to Settings > Deployment Settings)
──────────────────────────────────────────────────────────────

Proceed? [Enter/Y] / [C]ustomize Context / [Q]uit:
```

- Press **Enter** or `Y` to proceed.
- Press `C` to update deployment context before scanning.
- Press `Q` to cancel.

### Step 2 — Uploaded Report Detection

If you previously configured a ZAP report path (section 3.3), the tool will detect it:

```
📤 UNPROCESSED UPLOADED ZAP REPORT DETECTED
==================================================
File: C:\path\to\security_recommendations.json
Target URL: http://myapp.example.com

Options:
   1. Analyze this uploaded report now
   2. Run a fresh LIVE DAST scan instead
   0. Cancel and go back
```

- Choose `1` to analyze the existing report (no live scan needed).
- Choose `2` to run a new live scan with ZAP.

### Step 3 — Target URL Selection (Live Scan)

If running a live scan, you will be prompted to confirm or change the target:

```
📍 Target URL Options:
   1. Use current URL (http://myapp.example.com)
   2. Enter new URL
```

For multiple configured URLs:
```
   1. Scan all configured URLs (3 URLs)
   2. Scan single URL from list
   3. Enter new URL
```

### Step 4 — Connectivity Check

The tool automatically tests each target URL before launching ZAP:

```
🔍 Testing connectivity to target URL(s)...

[1/1] Testing http://myapp.example.com...
   ✅ DNS resolution successful for myapp.example.com
   ✅ Target URL is accessible (HTTP 200)
```

If a URL is unreachable, you will be asked whether to continue anyway.

### Step 5 — Scan Execution

ZAP launches and scans the target. Progress is shown in the terminal. Scan duration depends on the application size and the configured max scan time.

### Step 6 — Results & Reports

After the scan completes, AppSecAI processes the ZAP findings through its AI scoring engine and generates reports in the `generated_reports` folder inside your `CazeAppSecReport` directory.

---

## 6. Running an SCA Scan

**Path:** Main Menu → `2` Security Analysis → `3` SCA (Software Composition Analysis)

### Step 1 — Scan Review

Same confirmation screen as DAST (see Step 1 above), showing the configured SCA target.

### Step 2 — Target Selection

**If you have a GitHub repository configured**, the tool offers to reuse it:

```
💡 Detected configured github repository: https://github.com/owner/myapp
Do you want to run the Trivy SCA scan against this repository? (Y/n):
```

- `Y` — scans the configured repository directly.
- `n` — prompts you to configure a different target (image, local path, k8s, etc.).

**If multiple repositories are configured**, you get a batch menu:

```
Scan Target Options:
   1. Scan ALL configured repositories (3 repos)
   2. Select a specific repository from the list
   3. Custom target (Container Image, Local Path, K8s, etc.)
   4. Current directory (local files)
```

**If no repository is configured**, the tool uses the current Trivy settings (target type and path from section 4.1) and asks you to confirm:

```
📄 Current Trivy Settings:
   Target Type: fs
   Target Path: ./
Is this configuration correct? (Y/n):
```

### Step 3 — Scan Execution

Trivy runs against the selected target. For each target you will see:

```
🔄 SCANNING: https://github.com/owner/myapp
🚀 Executing: AppSecAI.exe scan --type sca --target-type repo --target https://...

✅ SCA completed successfully for owner/myapp (Clean).
```

or

```
⚠️ SCA completed for owner/myapp (Findings found).
```

### Step 4 — PDF Report Generation

After all targets are scanned, you are offered a PDF report:

```
Generate SCA Security Posture PDF report for these results? (Y/n):
```

- `Y` — generates a PDF in the `generated_reports` folder.
- `n` — skips PDF generation (JSON results are always saved to `cazelabs_output`).

After PDF generation:
```
📄 SCA PDF report generated: C:\...\CazeAppSecReport\generated_reports\sca_posture_report.pdf
🔍 Open reports directory? (y/N):
```

Enter `y` to open the folder in Windows Explorer.

---

## 7. Quick Reference — Navigation Commands

| Where you want to go | Command |
|---|---|
| Main Menu | `cd /` or `cd main` |
| Settings Menu | `cd settings` |
| DAST Configuration | `cd dast` |
| SCA Configuration | `cd sca` |
| Security Analysis | `cd scan` |
| Go back one level | `cd ..` |
| Go back two levels | `cd ../..` |
| Show available menus | `cd/list` |

**Full workflow — DAST setup and scan:**
```
Main Menu → 1 → 2 → 1 (set URL) → 5 (test connection) → 0 → 5 (save) → 0 → 2 → 2
```

**Full workflow — SCA setup and scan:**
```
Main Menu → 1 → 3 → 1 (set target) → 2 (set context) → 0 → 5 (save) → 0 → 2 → 3
```
