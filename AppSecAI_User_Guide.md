# AppSecAI CLI — User Guide

---

## Table of Contents

1. [General Overview & CLI Features](#1-general-overview--cli-features)
   - 1.1 [Help & Command Line Usage](#11-help--command-line-usage)
   - 1.2 [About AppSecAI](#12-about-appsecai)
2. [SAST Security Analysis](#2-sast-security-analysis)
   - 2.1 [Introduction](#21-introduction)
   - 2.2 [Prerequisites & System Requirements](#22-prerequisites--system-requirements)
   - 2.3 [Navigation Basics](#23-navigation-basics)
   - 2.4 [Step 1 — Generate a GitHub Personal Access Token](#24-step-1--generate-a-github-personal-access-token)
   - 2.5 [Step 2 — Set Up SonarQube](#25-step-2--set-up-sonarqube)
   - 2.6 [Step 3 — Configure sonar-project.properties & Run Scanner](#26-step-3--configure-sonar-projectproperties--run-scanner)
   - 2.7 [Step 4 — Initial Setup Wizard (CazeAppSecAI CLI)](#27-step-4--initial-setup-wizard-cazeappsecai-cli)
   - 2.8 [Step 5 — Configure SAST Settings in the CLI](#28-step-5--configure-sast-settings-in-the-cli)
   - 2.9 [Running the SAST Pipeline](#29-running-the-sast-pipeline)
   - 2.10 [Understanding the Vulnerability Report](#210-understanding-the-vulnerability-report)
   - 2.11 [Quick Reference — Navigation Commands](#211-quick-reference--navigation-commands)
   - 2.12 [Troubleshooting & Support](#212-troubleshooting--support)
3. [DAST Security Analysis](#3-dast-security-analysis)
   - 3.1 [Getting Started](#31-getting-started)
   - 3.2 [Navigation Basics](#32-navigation-basics)
   - 3.3 [DAST — Settings & Configuration](#33-dast--settings--configuration)
   - 3.4 [Saving Your Configuration](#34-saving-your-configuration)
   - 3.5 [Running a DAST Scan](#35-running-a-dast-scan)
   - 3.6 [Quick Reference — Navigation Commands](#36-quick-reference--navigation-commands)
4. [SCA Security Analysis](#4-sca-security-analysis)
   - 4.1 [Getting Started](#41-getting-started)
   - 4.2 [Navigation Basics](#42-navigation-basics)
   - 4.3 [SCA — Settings & Configuration](#43-sca--settings--configuration)
   - 4.4 [Saving Your Configuration](#44-saving-your-configuration)
   - 4.5 [Running an SCA Scan](#45-running-an-sca-scan)
   - 4.6 [Quick Reference — Navigation Commands](#46-quick-reference--navigation-commands)

---

## 1. General Overview & CLI Features

AppSecAI is an AI-powered security scanning platform delivered as a standalone executable (`python -m cli`). It bundles SAST (via SonarQube), DAST (via OWASP ZAP — built-in, no installation required), and SCA (via Trivy — built-in, no installation required) into a single tool, with AI-driven vulnerability remediation and automated GitHub pull request creation.

### Two Modes of Operation

Every time you launch AppSecAI you are presented with the startup menu:

```
╔══════════════════════════════════════════════════════════════╗
║                      CazeAppSecAI CLI                        ║
╚══════════════════════════════════════════════════════════════╝

How would you like to configure AppSecAI?
1. Run Scan using Configuration File (appsec_config.json)
2. Configure & Run Scan (Interactive Setup)
3. Help / Usage Guide
4. About AppSecAI

Select mode (1-4) [Default: 1]:
```

**Mode 1 — JSON Config File (Default)**
All settings are read from `appsec_config.json` in the same folder as the executable. Edit the file once, press Enter, and run scans immediately. Best for repeatable or automated workflows.

**Mode 2 — Interactive CLI**
A menu-driven wizard walks you through every setting interactively. Every change is **auto-saved to `.env` immediately** — no manual save step needed. Best for first-time setup or ad-hoc scans.

---

### 1.1 Help & Command Line Usage

#### From the Startup Menu

Type `3` at the startup menu and press Enter to view the built-in help guide.

#### From the Terminal

AppSecAI also exposes a full command-line interface. Run from a terminal in the folder containing `python -m cli`:

**Top-level help:**
```
python -m cli --help
```

**Command-specific help:**
```
python -m cli scan --help
python -m cli fix --help
python -m cli report --help
python -m cli config --help
python -m cli sca --help
```

**Global flags (available on every command):**

| Flag | Short | Description |
|------|-------|-------------|
| `--config` | `-c` | Config file path (default: `app_config.yaml`) |
| `--verbose` | `-v` | Enable verbose output |
| `--quiet` | `-q` | Suppress non-essential output |
| `--output-dir` | `-o` | Output directory (default: `AppSecAI_output`) |
| `--format` | `-f` | Output format: `json`, `csv`, `html`, `pdf` |

**Available commands:**

| Command | Description |
|---------|-------------|
| `app` | Launch the interactive menu-driven application |
| `scan` | Run a security scan (SAST, DAST, SCA, or combined) |
| `fix` | Generate and apply AI-powered vulnerability fixes |
| `report` | Generate security reports in multiple formats |
| `config` | Manage and validate configuration |
| `sca` | Analyze a Trivy JSON report directly |

**Example commands:**

```
python -m cli scan --type sast --target https://github.com/user/repo.git
python -m cli scan --type dast --target https://example.com
python -m cli scan --type both --target https://github.com/user/repo.git --dast-url https://app.example.com
python -m cli scan --type sca --target ./my-project
python -m cli sca --trivy-report trivy_output.json --export-json --export-pdf
python -m cli fix --input filtered_vulnerabilities.csv --create-prs
python -m cli report --input results.json --format html,pdf
python -m cli config --validate
python -m cli config --init
python -m cli config --show
python -m cli config --setup
```

---

### 1.2 About AppSecAI

Type `4` at the startup menu, or from inside the interactive app type `cd about` (or `cd cazelabs`), to view the About screen with product and company information.

AppSecAI is built by **CazeLabs**. OWASP ZAP (DAST) and Trivy (SCA) are bundled inside the application — no separate installation is needed for either tool.

---

## 2. SAST Security Analysis

### 2.1 Introduction

SAST (Static Application Security Testing) analyzes your source code without executing it.
AppSecAI uses **SonarQube** as its SAST engine. It clones your GitHub repository, runs the
SonarQube scanner, fetches results, applies AI-powered risk scoring, and produces a filtered
vulnerability report.

If SonarQube is not reachable, AppSecAI falls back to a **mock SAST analysis** automatically.

---

### 2.2 Prerequisites & System Requirements

| Requirement | Details |
|---|---|
| AppSecAI application | In a writable folder on your machine |
| GitHub repository | Public or private repo to scan |
| GitHub Personal Access Token | Required to clone repos and create PRs |
| SonarQube instance | Local or remote — see Section 2.5 |
| Internet access | To clone repos and reach SonarQube |

> If SonarQube is not set up, AppSecAI falls back to mock SAST. You can still explore the full workflow.

---

### 2.3 Navigation Basics

AppSecAI uses a `cd`-style navigation system. At any prompt you can type a `cd` command instead of a number.

The breadcrumb shown at every screen looks exactly like this (path is the folder where the EXE lives):

```
C:\Users\You\Desktop\CazeAppSecReport> Main Menu
```

As you navigate deeper it grows:

```
C:\Users\You\Desktop\CazeAppSecReport> Main Menu > Settings Menu > SonarQube Settings
```

**Navigation commands:**

| Command | Action |
|---|---|
| `cd settings` | Go to Settings Menu |
| `cd sonar` | Go to SonarQube Settings |
| `cd sast` | Go to SAST Configuration |
| `cd scan` | Go to Security Analysis |
| `cd threshold` | Go to Vulnerability Thresholds |
| `cd ..` or `cd/back` | Go back one level |
| `cd /` or `cd/main` | Go to Main Menu |
| `cd/list` | Show available menus from current location |
| `cd/help` | Show navigation help |
| `cd about` | Show About screen |

Tab completion is supported — press **Tab** after `cd ` to autocomplete menu names.
Command history is stored in `~/.caze_cli_history` (Up/Down arrow keys).

---

### 2.4 Step 1 — Generate a GitHub Personal Access Token

A GitHub PAT is required to clone private repositories, create pull requests with AI fixes,
and create GitHub issues for failed remediations.

1. Log in to https://github.com
2. Click your profile picture → **Settings**
3. Scroll to **Developer settings** → **Personal access tokens** → **Tokens (classic)**
4. Click **Generate new token (classic)**
5. Name it (e.g. `AppSecAI-Token`), set expiration (90 days recommended)
6. Select scopes: `repo` (required), `workflow` (if repo uses GitHub Actions)
7. Click **Generate token** — **copy it immediately**, it won't be shown again

---

### 2.5 Step 2 — Set Up SonarQube

The easiest way to run SonarQube locally is with Docker:

```
docker run -d --name sonarqube -p 9000:9000 sonarqube:community
```

Wait ~60 seconds then open `http://localhost:9000`.
Default credentials: username `admin`, password `admin` (you will be prompted to change it on first login).

**Create a SonarQube project:**

1. Click **Create Project** → **Manually**
2. Enter a **Project display name** and a **Project key** (e.g. `my-app-key`) — note the key down
3. Click **Set Up** → **Locally**
4. Generate a project token when prompted — note it down

> The **Project Key** is what you enter in AppSecAI as `sonar_project_key`.

---

### 2.6 Step 3 — Configure sonar-project.properties & Run Scanner

Create `sonar-project.properties` in the root of your repository:

```properties
sonar.projectKey=my-app-key
sonar.projectName=My Application
sonar.projectVersion=1.0
sonar.sources=.
sonar.host.url=http://localhost:9000
sonar.login=<your-sonarqube-project-token>
```

Then run the SonarQube Scanner from your project root:

```
sonar-scanner
```

Results will appear in the SonarQube dashboard at `http://localhost:9000`.

> AppSecAI can also trigger the scanner automatically when SonarQube is reachable.
> The manual step above pre-populates results before running AppSecAI.

---

### 2.7 Step 4 — Initial Setup Wizard (CazeAppSecAI CLI)

Launch `python -m cli`. The banner and startup menu appear:

```
╔══════════════════════════════════════════════════════════════╗
║                      CazeAppSecAI CLI                        ║
╚══════════════════════════════════════════════════════════════╝

How would you like to configure AppSecAI?
1. Run Scan using Configuration File (appsec_config.json)
2. Configure & Run Scan (Interactive Setup)
3. Help / Usage Guide
4. About AppSecAI

Select mode (1-4) [Default: 1]:
```

#### Mode 1 — JSON Config File

1. Open `appsec_config.json` (same folder as `python -m cli`) in any text editor
2. Fill in the SAST fields:

```json
{
  "github_token": "ghp_yourTokenHere",
  "github_repo": "username/repository-name",
  "github_branch": "main",
  "sonar_url": "http://localhost:9000",
  "sonar_username": "admin",
  "sonar_password": "your_sonar_password",
  "sonar_project_key": "my-app-key",
  "vulnerability_threshold": 3,
  "output_dir": "AppSecAI_output"
}
```

3. Save the file
4. Launch `python -m cli`, press **Enter** (or type `1`) → the app loads your config and goes to the main menu:

```
C:\...\CazeAppSecReport> Main Menu

┌─────────────────────────────────────────────────────────────┐
│                         MAIN MENU                           │
├─────────────────────────────────────────────────────────────┤
│  1.  Run SAST Risk Analysis                                 │
│  2.  Run DAST Risk Analysis                                 │
│  3.  Run SCA Risk Analysis                                  │
│  4.  View Configuration                                     │
│  0.  Exit                                                   │
└─────────────────────────────────────────────────────────────┘

Active Configuration:
- Mode: JSON (appsec_config.json)
- Repository: username/repository-name
- Target URL: Not Set
- GitHub Token: ✅ Set
- Context Profile: Default

👉 Select option (0-4) or command (cd <menu>, cd/, cd ..):
```

#### Mode 2 — Interactive CLI Wizard

1. Type `2` and press Enter → the wizard boots:

```
🔄 Booting Interactive CLI Wizard...
```

The **Advanced CLI Configuration Wizard** opens:

```
C:\...\CazeAppSecReport> Main Menu > Advanced Setup Wizard

┌─────────────────────────────────────────────────────────────┐
│             ADVANCED CLI CONFIGURATION WIZARD               │
├─────────────────────────────────────────────────────────────┤
│  1. SAST Configuration                                      │
│  2. DAST Configuration                                      │
│  3. SCA Configuration                                       │
│  4. Vulnerability Threshold Score                           │
│  0. Finish Configuration & Continue to Scans                │
└─────────────────────────────────────────────────────────────┘

👉 Select option (0-4):
```

2. Select `1` to configure SAST. The SAST Config sub-wizard opens:

```
C:\...\CazeAppSecReport> Main Menu > Advanced Setup Wizard > SAST Config

  1. Basic Config (GitHub Repo, Token, SonarQube)
  2. Context Modifiers (Deployment, Settings)
  0. Back

👉 Select option (0-2):
```

3. Select `1` for Basic Config — this takes you into the SAST Configuration menu (see Section 2.8).
4. When done, press `0` to go back, then `0` again to **Finish Configuration & Continue to Scans**.

The scan menu appears:

```
C:\...\CazeAppSecReport> Main Menu

┌─────────────────────────────────────────────────────────────┐
│                    SECURITY ANALYSIS                        │
├─────────────────────────────────────────────────────────────┤
│  1.  Run SAST Risk Analysis                                 │
│  2.  Run DAST Risk Analysis                                 │
│  3.  Run SCA Risk Analysis                                  │
│  0.  Exit                                                   │
└─────────────────────────────────────────────────────────────┘

👉 Select scan type (0-3) or command (cd <menu>, cd/, cd ..):
```

---

### 2.8 Step 5 — Configure SAST Settings in the CLI

From the **Main Menu** (JSON mode) or **Settings Menu** (either mode), navigate to SAST settings.

**From JSON mode main menu, type:**
```
cd settings
```

The Settings Menu appears:

```
C:\...\CazeAppSecReport> Main Menu > Settings Menu

┌─────────────────────────────────────────────────────────────┐
│                    SETTINGS MENU                            │
├─────────────────────────────────────────────────────────────┤
│  1.  SAST Configurations                                    │
│  2.  DAST Configurations                                    │
│  3.  SCA  Configurations                                    │
│  4.  General Application Settings                           │
│  5.  Save Configuration to .env                             │
│  0.  Back to Main Menu                                      │
└─────────────────────────────────────────────────────────────┘

Current Settings:
  GitHub Repositories: username/repo
  SonarQube URL:      http://localhost:9000
  DAST Target:        Not Set
  LLM Model:          WhiteRabbitNeo/Llama-3.1-WhiteRabbitNeo-2-8B:latest

👉 Select option (0-5) or command (cd <menu>, cd/, cd ..):
```

Select `1` or type `cd sast`. The SAST Configuration menu opens:

```
C:\...\CazeAppSecReport> Main Menu > Settings Menu > SAST Configuration

┌─────────────────────────────────────────────────────────────┐
│                [SAST] STATIC ANALYSIS SETTINGS              │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure GitHub Repository                            │
│  2.  Configure GitHub Token                                 │
│  3.  Configure SonarQube Settings                           │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘

👉 Select option (0-3):
```

Select `3` or type `cd sonar` to open SonarQube Settings:

```
C:\...\CazeAppSecReport> Main Menu > Settings Menu > SAST Configuration > SonarQube Settings

┌─────────────────────────────────────────────────────────────┐
│                    SONARQUBE SETTINGS                       │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure SonarQube URL                                │
│  2.  Configure Username                                     │
│  3.  Configure Password                                     │
│  4.  Configure Project Key                                  │
│  5.  View Current Settings                                  │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘

Current Settings:
  URL: http://localhost:9000
  Username: admin
  Password:  Not Set
  Project Key: Not Set

👉 Select option (0-5) or command (cd <menu>, cd/, cd ..):
```

- **Option 1** — Enter your SonarQube URL (e.g. `http://localhost:9000`). Must include `http://` or `https://`.
- **Option 2** — Enter your SonarQube username (default: `admin`).
- **Option 3** — Enter your SonarQube password (hidden input). You can also use a SonarQube token here.
- **Option 4** — Enter your SonarQube project key (e.g. `my-app-key`).
- **Option 5** — View all current SonarQube settings (password masked).

**Configure GitHub Repository (Option 1 in SAST Configuration):**

```
📍 Configure GitHub Repository
Enter repository (format: owner/repo, e.g. cazelabs/Caze_Test):
```

Enter in the format `username/repo-name` — no `https://github.com/` prefix.

**Configure GitHub Token (Option 2 in SAST Configuration):**

```
🔑 Configure GitHub Token
You can get a token from: https://github.com/settings/tokens
Enter GitHub token (input hidden):
```

**Configure Vulnerability Threshold:**

From Settings Menu, select `4` (General Application Settings) or type `cd threshold`:

```
C:\...\CazeAppSecReport> Main Menu > Settings Menu > General Settings

┌─────────────────────────────────────────────────────────────┐
│                  GENERAL APPLICATION SETTINGS               │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure AI/LLM Settings                              │
│  2.  Configure Vulnerability Thresholds                      │
│  3.  Configure Output Directory                              │
│  4.  Configure Deployment & Environment Context             │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘

👉 Select option (0-4):
```

Select `2`. Enter a threshold score between `0.0` and `10.0` (e.g. `3.0`).
Vulnerabilities scoring below this value are filtered out of reports.

---

### 2.9 Running the SAST Pipeline

#### Via JSON Config Mode

1. Ensure `appsec_config.json` has GitHub and SonarQube fields filled in
2. Launch `python -m cli`, press Enter (Mode 1)
3. From the Main Menu select **1. Run SAST Risk Analysis**
4. The pipeline runs:
   - Clones your repository from GitHub
   - Runs SonarQube analysis
   - Fetches vulnerability data from SonarQube API
   - Applies AI-powered risk scoring
   - Filters results by your threshold
   - Saves output to `CazeAppSecReport/AppSecAI_output/`

#### Via Interactive CLI Mode

1. Launch `python -m cli`, select Mode 2, complete the wizard
2. From the scan menu select **1. Run SAST Risk Analysis**

#### Via Direct CLI Command

```
python -m cli scan --type sast --target https://github.com/username/repo.git
```

With options:

```
python -m cli scan --type sast --target https://github.com/username/repo.git --branch develop --threshold 5 --export --output-dir my_results
```

**All `scan` flags for SAST:**

| Flag | Description |
|---|---|
| `--type sast` | Run SAST scan |
| `--target` | GitHub repository URL (required) |
| `--branch` | Branch to scan (e.g. `main`, `develop`) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--project-key` | SonarQube project key (overrides config) |
| `--github-token` | GitHub token (overrides config) |
| `--sonar-username` | SonarQube username (overrides config) |
| `--sonar-password` | SonarQube password (overrides config) |
| `--clone-dir` | Directory for cloning the repo (default: `cloned_repos`) |
| `--export` | Export results to files |
| `--output-dir` | Output directory (overrides config) |
| `--auto-fix` | Automatically run AI remediation after scan |
| `--interactive-pr` | Ask for confirmation before creating PRs (use with `--auto-fix`) |

---

### 2.10 Understanding the Vulnerability Report

Output files are saved to `CazeAppSecReport/AppSecAI_output/` (next to the EXE).

| File | Description |
|---|---|
| `raw_vulnerabilities.csv` | All SonarQube results before filtering |
| `filtered_vulnerabilities.csv` | Results that passed the threshold |
| `sast_report_<timestamp>.html` | HTML report (with `--export`) |
| `sast_report_<timestamp>.json` | JSON data (with `--export`) |

**Key fields in the report:**

| Field | Description |
|---|---|
| `id` | Unique vulnerability identifier |
| `type` | `BUG`, `VULNERABILITY`, or `CODE_SMELL` |
| `severity` | `Critical`, `High`, `Medium`, `Low` |
| `title` | Short description |
| `file_path` | Source file |
| `line_number` | Line number |
| `rule_key` | SonarQube rule ID |
| `risk_score` | AI-computed risk score (higher = more critical) |

---

### 2.11 Quick Reference — Navigation Commands

| Command | Action |
|---|---|
| `cd settings` | Open Settings Menu |
| `cd sast` | Open SAST Configuration |
| `cd sonar` | Open SonarQube Settings |
| `cd threshold` | Open Vulnerability Thresholds |
| `cd github` | Configure GitHub Repository |
| `cd token` | Configure GitHub Token |
| `cd output` | Configure Output Directory |
| `cd scan` | Open Security Analysis menu |
| `cd ..` | Go back one level |
| `cd /` | Return to Main Menu |
| `cd/list` | Show available menus |

---

### 2.12 Troubleshooting & Support

**SonarQube connection fails**
- Verify SonarQube is running and accessible at the configured URL
- Check username and password are correct
- Use Option 5 (View Current Settings) in SonarQube Settings to confirm values

**Repository clone fails**
- Verify your GitHub PAT has `repo` scope
- Confirm the repository format is `username/repo-name` (no `https://github.com/` prefix in JSON config)
- Check internet connection

**No vulnerabilities found**
- Lower `vulnerability_threshold` (try `1` or `2`)
- Verify the SonarQube project has completed analysis
- Confirm the correct `sonar_project_key` is configured

**Mock SAST running instead of real SonarQube**
- SonarQube credentials or URL are missing/invalid
- Configure via `cd sonar` or update `appsec_config.json`

**Output files not found**
- Reports save to `CazeAppSecReport/` folder next to the EXE
- Check terminal output for the exact path printed after the scan

---

## 3. DAST Security Analysis

### 3.1 Getting Started

DAST (Dynamic Application Security Testing) tests a **running** web application by sending
real HTTP requests and analyzing responses. AppSecAI uses **OWASP ZAP 2.16.1**, which is
**bundled inside the application** — no separate ZAP installation is required.

ZAP performs spider crawling, passive scanning, and active attack simulation against your
target URL. You only need to provide the URL of the running application.

**What you need before starting:**

| Requirement | Details |
|---|---|
| AppSecAI application | In a writable folder on your machine |
| Running web application | Accessible via a URL (http or https) |
| Target URL | Full URL of the app to scan (e.g. `https://app.example.com`) |

---

### 3.2 Navigation Basics

The breadcrumb displayed when navigating to DAST settings looks like this (path reflects your actual working directory):

```
F:\Iss-56\caze-code-sec-ai> Main Menu > Settings Menu > DAST Configuration
```

**Navigation shortcuts for DAST:**

| Command | Action |
|---|---|
| `cd settings` | Go to Settings Menu |
| `cd dast` | Go to DAST Configuration |
| `cd scan` | Go to Security Analysis |
| `cd ..` or `cd/back` | Go back one level |
| `cd /` or `cd/main` | Go to Main Menu |
| `cd/list` | Show available menus |

---

### 3.3 DAST — Settings & Configuration

#### Mode 1 — JSON Config File

Open `appsec_config.json` and fill in the DAST field:

```json
{
  "dast_url": "https://your-target-app.com",
  "vulnerability_threshold": 3,
  "output_dir": "AppSecAI_output"
}
```

To upload an existing ZAP HTML report instead of running a live scan, also set:

```json
{
  "zap_report_path": "C:\\path\\to\\security_recommendations.html"
}
```

Save the file, launch the application, press Enter (Mode 1).

#### Mode 2 — Interactive CLI

1. Launch the application, select Mode 2, complete the initial wizard
2. From the Advanced CLI Configuration Wizard select `2. DAST Configuration`:

```
F:\Iss-56\caze-code-sec-ai> Main Menu > Advanced Setup Wizard > DAST Config

  1. Basic Config (ZAP Target)
  2. Context Modifiers (Deployment, Settings)
  0. Back

👉 Select option (0-2):
```

3. Select `1` — this opens the DAST Configuration menu:

```
F:\Iss-56\caze-code-sec-ai> Main Menu > Advanced Setup Wizard > DAST Config > DAST Configuration

┌─────────────────────────────────────────────────────────────┐
│               [DAST] DYNAMIC ANALYSIS SETTINGS              │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure DAST Target URL(s)                           │
│  2.  Configure ZAP Scanner Settings                         │
│  3.  Configure ZAP Report Path (Upload)                     │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘

👉 Select option (0-3):
```

4. Select `0` to go back, then select `2` for Context Modifiers (Deployment Settings):

```
F:\Iss-56\caze-code-sec-ai> Main Menu > Advanced Setup Wizard > DAST Config > Deployment Settings

┌──────────────────────────────────────────────────────────────────────┐
│                  CONFIGURE DEPLOYMENT SETTINGS                       │
├──────────────────────────────────────────────────────────────────────┤
│  Options (type command or use cd):                                   │
│                                                                      │
│  1. product and version  - App name & version                        │
│     (cd product)                                                     │
│  2. environment          - Deployment type, General compliance       │
│     (cd env)                                                         │
│  3. runtime              - Container, monitoring & resource limits   │
│     (cd runtime)                                                     │
│  4. service              - Service auth & rate limiting              │
│     (cd service)                                                     │
│  5. security controls    - Security features (RBAC, WAF, MFA, etc.) │
│     (cd security, cd controls)                                       │
│  0. back or cd/..        - Return to Settings Menu                   │
└──────────────────────────────────────────────────────────────────────┘

👉 Select option (0-6), command, or cd navigation:
```

**Navigating to DAST settings from the main menu at any time:**

Type `cd settings` then `cd dast`, or directly `cd dast` from anywhere.

The DAST Configuration menu when accessed from Settings Menu:

```
F:\Iss-56\caze-code-sec-ai> Main Menu > Settings Menu > DAST Configuration

┌─────────────────────────────────────────────────────────────┐
│               [DAST] DYNAMIC ANALYSIS SETTINGS              │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure DAST Target URL(s)                           │
│  2.  Configure ZAP Scanner Settings                         │
│  3.  Configure ZAP Report Path (Upload)                     │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘

👉 Select option (0-3):
```

**Option 1 — Configure DAST Target URL(s)**

Selecting option `1` opens the URL configuration sub-menu:

```
📍 Configure DAST Target URL(s)

Options:
1. Set single URL
2. Set multiple URLs (one by one)
3. Set multiple URLs (comma-separated)
4. View current URLs
5. Clear all URLs

Select option (1-5):
```

- **Single URL** — Enter one target URL, e.g. `https://app.example.com`
- **Multiple URLs (one by one)** — Enter URLs one at a time; press Enter on an empty line to finish
- **Multiple URLs (comma-separated)** — Paste all URLs separated by commas
- **View current URLs** — Lists all configured DAST targets
- **Clear all URLs** — Removes all configured targets (asks for confirmation)

The URL must include the protocol (`http://` or `https://`). Invalid formats are rejected with an error.

**Option 2 — Configure ZAP Scanner Settings**

Displays the current ZAP scanner configuration:

```
🔧 ZAP Scanner Configuration:
• ZAP installation path: external/ZAP_2.16.1/
• Default scan policy: Default Policy
• Max scan time: 3600 seconds
• Spider max depth: 5
💡 These settings can be modified in app_config.yaml
```

ZAP is bundled at `external/ZAP_2.16.1/` inside the application — no external installation needed.
Advanced ZAP settings (scan policy, max scan time, spider depth) can be tuned in `app_config.yaml`.

**Option 3 — Configure ZAP Report Path (Upload)**

Use this option if you already have a ZAP HTML report and want AppSecAI to analyze it
without running a live scan:

```
📤 Configure ZAP Report Path (Upload for Analysis)
Current Path: Not set

Enter path to ZAP security_recommendations.html (or '0' to back):
```

Enter the full path to your existing ZAP HTML report file. AppSecAI will parse and
risk-score the findings from that report.

---

### 3.4 Saving Your Configuration

#### In JSON Config Mode

Changes to `appsec_config.json` are saved when you save the file. The app reads it fresh on each launch.

#### In Interactive CLI Mode

Every setting you change in the interactive menus is **auto-saved to `.env` immediately** — there is no separate save step. As soon as you confirm a value (e.g. enter a URL, token, or project key), it is written to the `.env` file in the application folder and will be loaded automatically on the next launch.

**Configuration priority (highest to lowest):**

1. Active environment variables (current session / CLI flags)
2. `appsec_config.json` (manual file edits)
3. `.env` file (auto-saved interactive settings)
4. `app_config.yaml` (system defaults)

---

### 3.5 Running a DAST Scan

#### Via JSON Config Mode

1. Set `dast_url` in `appsec_config.json`
2. Launch `python -m cli`, press Enter (Mode 1)
3. From the Main Menu select **2. Run DAST Risk Analysis**
4. The pipeline runs:
   - Launches the bundled OWASP ZAP 2.16.1 in headless mode
   - Spider crawls the target URL
   - Performs passive scanning
   - Performs active attack scanning
   - Generates a ZAP HTML report
   - Applies AI-powered risk scoring to findings
   - Filters results by your threshold
   - Saves output to `CazeAppSecReport/AppSecAI_output/`

#### Via Interactive CLI Mode

1. Launch `python -m cli`, select Mode 2, complete the wizard with DAST URL configured
2. From the scan menu select **2. Run DAST Risk Analysis**

#### Via Direct CLI Command

```
python -m cli scan --type dast --target https://your-target-app.com
```

With options:

```
python -m cli scan --type dast --target https://your-target-app.com --threshold 3 --timeout 1800 --export --output-dir dast_results
```

**Combined SAST + DAST scan:**

```
python -m cli scan --type both --target https://github.com/username/repo.git --dast-url https://your-target-app.com
```

**All `scan` flags for DAST:**

| Flag | Description |
|---|---|
| `--type dast` | Run DAST scan |
| `--target` | Target web application URL (required) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--timeout` | Scan timeout in seconds (overrides config) |
| `--export` | Export results to files |
| `--output-dir` | Output directory (overrides config) |
| `--auto-fix` | Automatically run AI remediation after scan |
| `--interactive-pr` | Ask for confirmation before creating PRs (use with `--auto-fix`) |

**DAST output files** (saved to `CazeAppSecReport/AppSecAI_output/`):

| File | Description |
|---|---|
| `zap_report_<timestamp>.html` | Full ZAP HTML report |
| `dast_vulnerabilities_<timestamp>.csv` | Normalized vulnerability list |
| `dast_report_<timestamp>.json` | JSON data (with `--export`) |

**Key DAST vulnerability fields:**

| Field | Description |
|---|---|
| `id` | Unique vulnerability identifier |
| `severity` | `Critical`, `High`, `Medium`, `Low` |
| `title` | Vulnerability name (e.g. `SQL Injection`, `XSS`) |
| `url` | Affected URL |
| `parameter` | Affected parameter or input field |
| `confidence` | ZAP confidence level |
| `solution` | Recommended fix |
| `cwe_id` | CWE identifier |
| `risk_score` | AI-computed risk score |
| `ai_recommendation` | AI-generated remediation advice |

---

### 3.6 Quick Reference — Navigation Commands

| Command | Action |
|---|---|
| `cd settings` | Open Settings Menu |
| `cd dast` | Open DAST Configuration |
| `cd threshold` | Open Vulnerability Thresholds |
| `cd scan` | Open Security Analysis menu |
| `cd output` | Configure Output Directory |
| `cd ..` | Go back one level |
| `cd /` | Return to Main Menu |
| `cd/list` | Show available menus |

---

## 4. SCA Security Analysis

### 4.1 Getting Started

SCA (Software Composition Analysis) identifies known vulnerabilities in third-party dependencies and open-source libraries. AppSecAI uses **Trivy** as its SCA engine — **Trivy is bundled inside AppSecAI, no separate installation is needed**.

**SCA workflow:**

1. AppSecAI runs Trivy internally against your project
2. Trivy generates a JSON report
3. AppSecAI ingests the report, normalizes findings, applies AI-powered risk scoring
4. Prioritized output is saved to the output directory

**What you need:**

| Requirement | Details |
|---|---|
| AppSecAI application | In a writable folder |
| Project to scan | Local directory, container image, or repository |
| Internet access | For Trivy to download vulnerability databases |

---

### 4.2 Navigation Basics

From the interactive app, navigate to SCA settings:

| Command | Action |
|---|---|
| `cd settings` | Go to Settings Menu |
| `cd sca` | Go to SCA Configuration |
| `cd scan` | Go to Security Analysis menu |
| `cd ..` | Go back one level |
| `cd /` | Go to Main Menu |
| `cd/list` | Show available menus |

---

### 4.3 SCA — Settings & Configuration

#### Step 1: Configure SCA Target

From the **Main Menu** (JSON mode) or after completing the wizard (Interactive mode), navigate to SCA settings.

**From JSON mode:**
```
cd settings
```

Then select `3` or type `cd sca`. The SCA Configuration menu opens:

```
C:\...\CazeAppSecReport> Main Menu > Settings Menu > SCA Configuration

┌─────────────────────────────────────────────────────────────┐
│             [SCA] SOFTWARE COMPOSITION SETTINGS             │
├─────────────────────────────────────────────────────────────┤
│  1.  Configure SCA (Trivy) Scan Settings                    │
│  2.  Configure GitHub Token                                 │
│  0.  Back to Settings Menu                                  │
└─────────────────────────────────────────────────────────────┘

👉 Select option (0-2):
```

Select `1` to configure Trivy scan settings:

```
📦 Configure SCA (Trivy) Scan Settings
Current Target Type: fs
Current Target Path: ./

Enter target type (fs, image, repo, k8s, container, vm, rootfs) [fs]:
```

**Target types:**

| Type | Description | Example |
|---|---|---|
| `fs` | Local filesystem directory | `./my-project` or `.` |
| `image` | Container image | `python:3.9`, `ubuntu:latest` |
| `repo` | Remote Git repository | `https://github.com/user/repo` or `user/repo` |
| `k8s` | Kubernetes cluster | `cluster` |
| `container` | Running container | `<container_id>` |
| `vm` | Virtual machine image | `<vm_image_path>` |
| `rootfs` | Root filesystem | `<rootfs_path>` |

Enter the target type (e.g. `fs`), then enter the target path:

```
Enter target path/name (e.g., ./ , python:3.9 , cluster , <container_id>) [./]:
```

Examples:
- For local project: `./` or `./my-project`
- For container image: `python:3.9` or `nginx:latest`
- For GitHub repo: `https://github.com/username/repo` or `username/repo`

The settings are auto-saved to the `.env` file immediately upon confirmation.

#### Step 2: Configure SCA Context (Optional but Recommended)

The SCA context provides AppSecAI with information about your project environment, which improves AI-powered risk scoring accuracy.

From Settings Menu, select `4` (General Application Settings), then select `4` (Configure Deployment & Environment Context).

Or navigate directly:
```
cd settings
cd sca context
```

The SCA Context Settings menu opens:

```
C:\...\CazeAppSecReport> Main Menu > Settings Menu > SCA Context Settings

┌──────────────────────────────────────────────────────────────────────┐
│                  CONFIGURE SCA CONTEXT SETTINGS                      │
├──────────────────────────────────────────────────────────────────────┤
│  Options (type command or use cd):                                   │
│                                                                      │
│  1. dependency management  - Update frequency, SBOM, lock files      │
│                                                                      │
│  2. package sources        - Registry, signature verification        │
│                                                                      │
│  3. vulnerability response - Patching, monitoring, emergency process │
│                                                                      │
│  4. build pipeline         - SLSA, hash verification, reproducibility│
│                                                                      │
│  5. runtime behavior       - Sandboxing, isolation, network access   │
│                                                                      │
│  6. ecosystem              - Language version, package manager       │
│                                                                      │
│  7. compliance             - Standards (SOC2, HIPAA, PCI-DSS, etc.)  │
│                                                                      │
│  0. back or cd/..          - Return to Settings Menu                 │
└──────────────────────────────────────────────────────────────────────┘

👉 Select option (0-7), command, or cd navigation:
```

Each sub-menu lets you configure context that influences vulnerability scoring:

**1. Dependency Management**
- Update frequency (daily, weekly, monthly)
- Whether lock files are enforced
- Whether automated dependency updates are enabled
- Dependency pinning strategy (exact, minor, major)
- Whether SBOM generation is enabled

**2. Package Sources**
- Whether a private registry is used
- Whether package signature verification is enabled
- Whether only trusted sources are allowed

**3. Vulnerability Response**
- Mean time to patch (e.g. `< 7d`, `7-30d`, `> 30d`)
- Vulnerability monitoring frequency
- Whether an emergency patch process exists

**4. Build Pipeline**
- Whether dependency caching is used
- Whether builds are reproducible
- Whether dependency hash verification is enabled
- SLSA level (e.g. `slsa1`, `slsa2`, `slsa3`)

**5. Runtime Behavior**
- Whether dependency isolation is enabled
- Whether sandboxing is enabled
- Whether dynamic loading is restricted

**6. Ecosystem**
- Primary language (e.g. `javascript`, `python`, `java`)
- Package manager (e.g. `npm`, `pip`, `maven`)
- Whether it is a monorepo

**7. Compliance**
- SOC2, HIPAA, PCI-DSS, GDPR, ISO 27001 requirements

These settings are stored in the `AppSecAI.sca_context` section of `appsec_config.json`.

---

### 4.4 Saving Your Configuration

#### In JSON Config Mode

All changes to `appsec_config.json` are saved when you save the file. The app reads it fresh on each launch.

#### In Interactive CLI Mode

Every setting you change in the interactive menus is **auto-saved to `.env` immediately**. There is no separate save step. As soon as you confirm a value, it is written to the `.env` file in the application folder and loaded automatically on the next launch.

Alternatively, manually edit `appsec_config.json` to persist SCA context settings. The JSON config takes priority over the `.env` file.

---

### 4.5 Running an SCA Scan

#### Via JSON Config Mode

1. Ensure `appsec_config.json` has `sca_target_type` and `sca_target_path` set (or configure via the interactive menu)
2. Launch `python -m cli`, press Enter (Mode 1)
3. From the Main Menu select **3. Run SCA Risk Analysis**
4. AppSecAI processes the scan:
   - Runs Trivy internally against your target
   - Reads and parses the Trivy JSON report
   - Normalizes vulnerabilities, misconfigurations, and secrets
   - Extracts CVSS scores and CWE identifiers
   - Applies AI-powered risk scoring using your SCA context
   - Filters results by your threshold
   - Saves prioritized output to `CazeAppSecReport/AppSecAI_output/`

#### Via Interactive CLI Mode

1. Launch `python -m cli`, select Mode 2, complete the wizard
2. From the scan menu select **3. Run SCA Risk Analysis**

#### Via Direct CLI Command (scan subcommand)

```
python -m cli scan --type sca --target ./my-project
```

With options:

```
python -m cli scan --type sca --target ./my-project --target-type fs --threshold 3 --export --output-dir sca_results
```

**All `scan --type sca` flags:**

| Flag | Description |
|---|---|
| `--type sca` | Run SCA scan |
| `--target` | Target path or URL to scan |
| `--target-type` | Target type: `fs`, `image`, `repo`, `k8s`, `vm`, `rootfs` (default: `fs`) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--export` | Export results to files |
| `--output-dir` | Output directory (overrides config) |

#### Via Direct CLI Command (sca subcommand - for existing Trivy reports)

If you already have a Trivy JSON report, use the `sca` subcommand directly:

```
python -m cli sca --trivy-report trivy_report.json
```

With export options:

```
python -m cli sca --trivy-report trivy_report.json --threshold 3 --output-dir sca_results --export-json --export-pdf
```

**All `sca` subcommand flags:**

| Flag | Description |
|---|---|
| `--trivy-report` | Path to Trivy JSON report file (required) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--output-dir` | Output directory for SCA results (overrides config) |
| `--export-json` | Export prioritized SCA findings to JSON |
| `--export-pdf` | Generate SCA security posture PDF report |

**What AppSecAI analyzes in the Trivy report:**

| Finding Type | Description |
|---|---|
| Vulnerabilities | Known CVEs in package dependencies |
| Misconfigurations | Infrastructure and configuration issues |
| Secrets | Exposed credentials, API keys, tokens |

**SCA output files:**

| File | Description |
|---|---|
| `sca_prioritized_<timestamp>.csv` | Prioritized vulnerability list |
| `sca_findings_<timestamp>.json` | JSON findings (with `--export-json`) |
| `sca_report_<timestamp>.pdf` | PDF security posture report (with `--export-pdf`) |

**Key SCA vulnerability fields:**

| Field | Description |
|---|---|
| `id` | Unique vulnerability identifier |
| `source_id` | CVE or finding ID from Trivy |
| `scanner` | Scanner that found the issue (e.g. `trivy`) |
| `target` | File or component where the issue was found |
| `severity` | `Critical`, `High`, `Medium`, `Low` |
| `package_name` | Affected package name |
| `installed_version` | Currently installed version |
| `fixed_version` | Version that fixes the vulnerability |
| `cvss_score` | CVSS base score |
| `cwe_id` | CWE identifier |
| `risk_score` | AI-computed risk score (higher = more critical) |
| `risk_level` | `Critical`, `High`, `Medium`, `Low`, `Info` |
| `category` | Vulnerability category (e.g. `injection`, `crypto`, `auth`) |
| `ai_justification` | AI-generated explanation of why this is prioritized |

**Terminal summary example:**

```
[PRIORITIZATION] Processing 142 vulnerabilities with threshold: 3.0
SCA Analysis Complete
   Scan ID: sca_20260508_151234
   Total findings in report: 142
   After threshold filtering (score >= 3.0): 38
   Critical: 6  |  High: 18  |  Medium: 12  |  Low: 2
   Output: CazeAppSecReport/AppSecAI_output/sca_prioritized_20260508_151234.csv
```

---

### 4.6 Quick Reference — Navigation Commands

| Command | Action |
|---|---|
| `cd settings` | Open Settings Menu |
| `cd sca` | Open SCA Configuration |
| `cd sca context` | Open SCA Context Settings |
| `cd dependency` | Open Dependency Management settings |
| `cd packages` | Open Package Sources settings |
| `cd response` | Open Vulnerability Response settings |
| `cd build` | Open Build Pipeline settings |
| `cd runtime` | Open Runtime Behavior settings |
| `cd ecosystem` | Open Ecosystem settings |
| `cd compliance` | Open Compliance settings |
| `cd threshold` | Open Vulnerability Thresholds |
| `cd scan` | Open Security Analysis menu |
| `cd output` | Configure Output Directory |
| `cd ..` | Go back one level |
| `cd /` | Return to Main Menu |
| `cd/list` | Show available menus |

---

