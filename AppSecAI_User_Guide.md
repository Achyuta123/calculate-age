# AppSecAI CLI — User Guide

---

## Table of Contents

1. [General Overview and CLI Features](#1-general-overview--cli-features)
   - 1.1 [Help and Command Line Usage](#11-help--command-line-usage)
   - 1.2 [About AppSecAI](#12-about-appsecai)
2. [SAST Security Analysis](#2-sast-security-analysis)
   - 2.1 [Introduction](#21-introduction)
   - 2.2 [Prerequisites and System Requirements](#22-prerequisites--system-requirements)
   - 2.3 [Navigation Basics](#23-navigation-basics)
   - 2.4 [Step 1 — Generate a GitHub Personal Access Token](#24-step-1--generate-a-github-personal-access-token)
   - 2.5 [Step 2 — Set Up SonarQube with Docker](#25-step-2--set-up-sonarqube-with-docker)
   - 2.6 [Step 3 — Configure sonar-project.properties and Run Scanner](#26-step-3--configure-sonar-projectproperties--run-scanner)
   - 2.7 [Step 4 — Initial Setup Wizard (CazeAppSecAI CLI)](#27-step-4--initial-setup-wizard-cazeappsecai-cli)
   - 2.8 [Step 5 — Configure SAST Settings in the CLI](#28-step-5--configure-sast-settings-in-the-cli)
   - 2.9 [Running the SAST Pipeline](#29-running-the-sast-pipeline)
   - 2.10 [Understanding the Vulnerability Report](#210-understanding-the-vulnerability-report)
   - 2.11 [Quick Reference — Navigation Commands](#211-quick-reference--navigation-commands)
   - 2.12 [Troubleshooting and Support](#212-troubleshooting--support)
3. [DAST Security Analysis](#3-dast-security-analysis)
   - 3.1 [Getting Started](#31-getting-started)
   - 3.2 [Navigation Basics](#32-navigation-basics)
   - 3.3 [DAST — Settings and Configuration](#33-dast--settings--configuration)
   - 3.4 [Saving Your Configuration](#34-saving-your-configuration)
   - 3.5 [Running a DAST Scan](#35-running-a-dast-scan)
   - 3.6 [Quick Reference — Navigation Commands](#36-quick-reference--navigation-commands)
4. [SCA Security Analysis](#4-sca-security-analysis)
   - 4.1 [Getting Started](#41-getting-started)
   - 4.2 [Navigation Basics](#42-navigation-basics)
   - 4.3 [SCA — Settings and Configuration](#43-sca--settings--configuration)
   - 4.4 [Saving Your Configuration](#44-saving-your-configuration)
   - 4.5 [Running an SCA Scan](#45-running-an-sca-scan)
   - 4.6 [Quick Reference — Navigation Commands](#46-quick-reference--navigation-commands)

---

## 1. General Overview & CLI Features

AppSecAI is an AI-powered security scanning platform delivered as a standalone executable (AppSecAI.exe). It combines Static Application Security Testing (SAST), Dynamic Application Security Testing (DAST), and Software Composition Analysis (SCA) into a single CLI tool, with AI-driven vulnerability remediation and automated GitHub pull request creation.

### Two Modes of Operation

AppSecAI supports two distinct operating modes. Both modes are available every time you launch the application.

**Mode 1 — JSON Config File Mode (Default)**

All settings are pre-configured in ppsec_config.json, which sits in the same folder as the executable. You edit this file once, then launch the app and run scans immediately without answering any prompts. This is the recommended mode for repeatable, automated, or CI/CD workflows.

**Mode 2 — Interactive CLI Mode**

A menu-driven wizard walks you through every setting interactively. You answer prompts for GitHub credentials, SonarQube details, target URLs, and thresholds. Settings can optionally be saved to a .env file for future sessions. This mode is ideal for first-time setup or ad-hoc scans.

### Launching the Application

Double-click AppSecAI.exe or run it from a terminal:

`
AppSecAI.exe
`

Or from a terminal in the same folder:

`
AppSecAI.exe app
`

On first launch the banner appears, followed by the startup menu:

`
╔══════════════════════════════════════════════════════════════╗
║                      CazeAppSecAI CLI                        ║
╚══════════════════════════════════════════════════════════════╝

How would you like to configure AppSecAI?
1. Run Scan using Configuration File (appsec_config.json)
2. Configure & Run Scan (Interactive Setup)
3. Help / Usage Guide
4. About AppSecAI

Select mode (1-4) [Default: 1]:
`

Press **Enter** (or type 1) to use JSON Config mode. Type 2 to enter Interactive mode.

---

### 1.1 Help & Command Line Usage

#### From the Startup Menu

Type 3 at the startup menu and press Enter to view the built-in help guide. It covers all available commands, flags, and usage examples.

#### From the Terminal (Direct CLI)

AppSecAI also exposes a full command-line interface. Run any of the following from a terminal in the folder containing AppSecAI.exe:

**Show top-level help:**

`
AppSecAI.exe --help
`

**Show help for a specific command:**

`
AppSecAI.exe scan --help
AppSecAI.exe fix --help
AppSecAI.exe report --help
AppSecAI.exe config --help
AppSecAI.exe sca --help
`

**Global flags available on every command:**

| Flag | Short | Description |
|------|-------|-------------|
| --config | -c | Path to config file (default: pp_config.yaml) |
| --verbose | -v | Enable verbose/debug output |
| --quiet | -q | Suppress non-essential output |
| --output-dir | -o | Output directory for results (default: AppSecAI_output) |
| --format | -f | Output format: json, csv, html, pdf |

**Available top-level commands:**

| Command | Description |
|---------|-------------|
| pp | Launch the interactive menu-driven application |
| scan | Run a security scan (SAST, DAST, SCA, or combined) |
| ix | Generate and apply AI-powered vulnerability fixes |
| 
eport | Generate security reports in multiple formats |
| config | Manage and validate configuration |
| sca | Analyze a Trivy JSON report directly |

**Example commands:**

`
# SAST scan of a GitHub repository
AppSecAI.exe scan --type sast --target https://github.com/user/repo.git

# DAST scan of a web application
AppSecAI.exe scan --type dast --target https://example.com

# Combined SAST + DAST scan
AppSecAI.exe scan --type both --target https://github.com/user/repo.git --dast-url https://app.example.com

# SCA scan using Trivy output
AppSecAI.exe scan --type sca --target ./my-project

# Analyze an existing Trivy JSON report
AppSecAI.exe sca --trivy-report trivy_output.json --export-json --export-pdf

# Generate AI fixes and create GitHub PRs
AppSecAI.exe fix --input filtered_vulnerabilities.csv --create-prs

# Generate an HTML + PDF report
AppSecAI.exe report --input results.json --format html,pdf

# Validate current configuration
AppSecAI.exe config --validate

# Initialize a fresh appsec_config.json template
AppSecAI.exe config --init

# Show current active configuration
AppSecAI.exe config --show

# Run the interactive setup wizard
AppSecAI.exe config --setup
`

---

### 1.2 About AppSecAI

Type 4 at the startup menu to view the About screen. It displays product information, version, and company details.

You can also navigate to it from inside the interactive app at any time:

`
cd about
`

or

`
cd cazelabs
`

AppSecAI is built by **CazeLabs**. It integrates SonarQube (SAST), OWASP ZAP (DAST), Trivy (SCA), and an AI/LLM engine for automated remediation and GitHub pull request generation.

---
## 2. SAST Security Analysis

### 2.1 Introduction

SAST (Static Application Security Testing) analyzes your source code without executing it. AppSecAI uses SonarQube as its SAST engine. It clones your GitHub repository, runs the SonarQube scanner against it, fetches the results, applies AI-powered risk scoring, and produces a filtered vulnerability report.

If SonarQube is not available or not configured, AppSecAI automatically falls back to a mock SAST analysis so you can still explore the workflow.

---

### 2.2 Prerequisites and System Requirements

Before running a SAST scan, ensure the following are in place:

| Requirement | Details |
|-------------|----------|
| AppSecAI.exe | Present in a writable folder on your machine |
| GitHub repository | A public or private repo you want to scan |
| GitHub Personal Access Token | Required to clone private repos and create PRs |
| SonarQube instance | Local (container recommended) or remote server |
| Container runtime | Required if running SonarQube locally |
| Java 17+ | Required by the SonarQube scanner |
| Internet access | Required to clone repos and reach SonarQube |

> Note: If you do not have SonarQube set up, AppSecAI will fall back to mock SAST. You can still complete the full workflow and see sample output.

---

### 2.3 Navigation Basics

AppSecAI uses a `cd`-style navigation system inside the interactive menus. You can type menu names or shortcuts at any prompt.

| Command | Action |
|---------|--------|
| `cd settings` | Go to Settings Menu |
| `cd sast` | Go to SAST Configuration |
| `cd sonar` | Go to SonarQube Settings |
| `cd scan` | Go to Security Analysis menu |
| `cd ..` | Go back one level |
| `cd /` | Go to Main Menu |
| `cd/list` | Show available menus from current location |
| `cd/help` | Show navigation help |
| `cd about` | Show About screen |

Tab completion is supported. Press Tab after typing `cd ` to see available options. Command history is stored in `~/.caze_cli_history` and accessible with the Up/Down arrow keys.

---

### 2.4 Step 1 - Generate a GitHub Personal Access Token

A GitHub Personal Access Token (PAT) is required to clone private repositories, create pull requests with AI-generated fixes, and create GitHub issues for failed remediations.

**Steps to generate a PAT:**

1. Log in to https://github.com
2. Click your profile picture (top-right) then **Settings**
3. Scroll down and click **Developer settings** in the left sidebar
4. Click **Personal access tokens** then **Tokens (classic)**
5. Click **Generate new token** then **Generate new token (classic)**
6. Give it a descriptive name, e.g. `AppSecAI-Token`
7. Set an expiration (90 days recommended)
8. Select the following scopes:
   - `repo` (full repository access, required for cloning and PRs)
   - `workflow` (if your repo uses GitHub Actions)
9. Click **Generate token**
10. **Copy the token immediately** - it will not be shown again

Keep this token ready. You will enter it during the setup wizard or in `appsec_config.json`.

---

### 2.5 Step 2 - Set Up SonarQube

The easiest way to run SonarQube locally is with a container runtime (e.g., Podman or Docker).

**Start SonarQube (example using container CLI):**

```
container-run -d --name sonarqube -p 9000:9000 sonarqube:community
```

Wait about 60 seconds for SonarQube to start, then open `http://localhost:9000` in your browser.

**Default credentials:**
- Username: `admin`
- Password: `admin`

On first login, SonarQube will prompt you to change the password. Set a new password and note it down.

**Create a project in SonarQube:**

1. Click **Create Project** then **Manually**
2. Enter a **Project display name** (e.g., `my-app`)
3. Enter a **Project key** (e.g., `my-app-key`) and note this down
4. Click **Set Up**
5. Choose **Locally** as the analysis method
6. Generate a project token when prompted and note it down (this is separate from your GitHub PAT)
7. Select your project language/build tool

> Tip: The Project Key is what you will enter in AppSecAI as `sonar_project_key`.

---

### 2.6 Step 3 - Configure sonar-project.properties and Run Scanner

To analyze your code, SonarQube needs a `sonar-project.properties` file in the root of your repository.

**Create `sonar-project.properties` in your repo root:**

```properties
sonar.projectKey=my-app-key
sonar.projectName=My Application
sonar.projectVersion=1.0
sonar.sources=.
sonar.host.url=http://localhost:9000
sonar.login=<your-sonarqube-project-token>
```

Replace `my-app-key` with your actual project key and `<your-sonarqube-project-token>` with the token generated in SonarQube.

**Run the SonarQube Scanner from your project root:**

```
sonar-scanner
```

After the scan completes, results will appear in the SonarQube dashboard at `http://localhost:9000`.

> Note: AppSecAI can also trigger the scanner automatically during the SAST pipeline if SonarQube is reachable. The manual step above is for pre-populating results before running AppSecAI.

---

### 2.7 Step 4 - Initial Setup Wizard (CazeAppSecAI CLI)

Launch AppSecAI and choose your mode.

#### Mode 1: JSON Config File

1. Open `appsec_config.json` (located in the same folder as `AppSecAI.exe`) in any text editor
2. Fill in the SAST-relevant fields:

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
4. Launch `AppSecAI.exe`
5. Press **Enter** (or type `1`) to select **Run Scan using Configuration File**
6. The app loads your config and proceeds to the main scan menu

#### Mode 2: Interactive CLI Wizard

1. Launch `AppSecAI.exe`
2. Type `2` and press Enter to select **Configure and Run Scan (Interactive Setup)**
3. The wizard starts and prompts for GitHub credentials:

```
Enter your GitHub Personal Access Token (hidden):
Enter your repository (format: username/repo-name):
```

4. Enter your GitHub PAT (input is hidden for security)
5. Enter your repository in the format `username/repo-name`
6. When prompted for SonarQube setup, type `y` to configure it or press Enter to skip

If you type `y`, you will be prompted for:
- SonarQube URL (press Enter to accept default `http://localhost:9000`)
- SonarQube Username (press Enter for `admin`)
- SonarQube Password (hidden input)
- Project Key (e.g., `my-app-key`)

7. Enter the output directory (press Enter for default `AppSecAI_output`)
8. Enter a vulnerability threshold score (e.g., `3`)
9. Press Enter to save settings to `.env` for future sessions

The wizard completes and drops you into the scan menu.

---

### 2.8 Step 5 - Configure SAST Settings in the CLI

In Interactive mode, you can configure SAST settings through the Settings menu at any time.

**Navigate to SonarQube settings:**

```
cd settings
```

Then:

```
cd sonar
```

The SonarQube Settings menu appears:

```
SonarQube Settings
Current Location: Main Menu > Settings Menu > SonarQube Settings

1. Configure SonarQube URL
2. Configure SonarQube Username
3. Configure SonarQube Password
4. Configure Project Key
5. Test SonarQube Connection
6. Show Current Settings
0. Back
```

Select each option to update the corresponding value.

- **Option 5** verifies that AppSecAI can reach your SonarQube server with the provided credentials before running a scan.
- **Option 6** displays the currently active SonarQube configuration (password is masked).

**Configure the vulnerability threshold:**

From the Settings menu, type `cd threshold`. Select option `1` and enter a numeric threshold (e.g., `3`). Vulnerabilities with a risk score below this value are excluded from reports.

---

### 2.9 Running the SAST Pipeline

#### Via JSON Config Mode

1. Ensure `appsec_config.json` is configured with your GitHub and SonarQube details
2. Launch `AppSecAI.exe` and press Enter to select Mode 1
3. From the main menu, select **1. Run SAST Scan**
4. The pipeline runs automatically:
   - Clones your repository from GitHub
   - Runs SonarQube analysis
   - Fetches vulnerability data from SonarQube
   - Applies AI-powered risk scoring
   - Filters results by your threshold
   - Saves output files to `AppSecAI_output/`

#### Via Interactive CLI Mode

1. Launch `AppSecAI.exe` and select Mode 2
2. Complete the setup wizard (Section 2.7)
3. From the scan menu, type `1` or `cd sast` to run the SAST scan

#### Via Direct CLI Command

```
AppSecAI.exe scan --type sast --target https://github.com/username/repo.git
```

With additional options:

```
AppSecAI.exe scan --type sast --target https://github.com/username/repo.git --branch develop --threshold 5 --export --output-dir my_results
```

**All `scan` flags for SAST:**

| Flag | Description |
|------|-------------|
| `--type sast` | Run SAST scan |
| `--target` | GitHub repository URL (required) |
| `--branch` | Branch to scan (e.g., `main`, `develop`) |
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

After a SAST scan, AppSecAI produces output files in the configured output directory (default: `AppSecAI_output/` next to the executable, or `CazeAppSecReport/` when running as EXE).

**Output files:**

| File | Description |
|------|-------------|
| `raw_vulnerabilities.csv` | All vulnerabilities returned by SonarQube before filtering |
| `filtered_vulnerabilities.csv` | Vulnerabilities that passed the threshold filter |
| `sast_report_<timestamp>.html` | HTML vulnerability report (when `--export` is used) |
| `sast_report_<timestamp>.json` | JSON vulnerability data (when `--export` is used) |

**Key vulnerability fields:**

| Field | Description |
|-------|-------------|
| `id` | Unique vulnerability identifier |
| `type` | Vulnerability type (e.g., `BUG`, `VULNERABILITY`, `CODE_SMELL`) |
| `severity` | Severity level: `Critical`, `High`, `Medium`, `Low` |
| `title` | Short description of the issue |
| `file_path` | Source file where the issue was found |
| `line_number` | Line number in the source file |
| `rule_key` | SonarQube rule identifier |
| `risk_score` | AI-computed risk score (higher = more critical) |

**Terminal summary example:**

```
SAST Scan Complete
   Scan ID: sast_20260508_143022
   Total vulnerabilities found: 47
   After threshold filtering (score >= 3): 23
   Critical: 4  |  High: 9  |  Medium: 8  |  Low: 2
   Output: AppSecAI_output/filtered_vulnerabilities.csv
```

---

### 2.11 Quick Reference - Navigation Commands

| Command | Action |
|---------|--------|
| `cd settings` | Open Settings Menu |
| `cd sonar` | Open SonarQube Settings |
| `cd threshold` | Open Vulnerability Thresholds |
| `cd scan` | Open Security Analysis menu |
| `cd sast` | Navigate to SAST Configuration |
| `cd github` | Configure GitHub Repository |
| `cd token` | Configure GitHub Token |
| `cd output` | Configure Output Directory |
| `cd ..` | Go back one level |
| `cd /` | Return to Main Menu |
| `cd/list` | Show available menus |
| `cd/help` | Show navigation help |

---

### 2.12 Troubleshooting and Support

**SonarQube connection fails:**
- Verify your container runtime is running and SonarQube is accessible at `http://localhost:9000`
- Check your username and password are correct
- Use Option 5 (Test SonarQube Connection) in the SonarQube Settings menu

**Repository clone fails:**
- Verify your GitHub PAT has `repo` scope
- Confirm the repository format is `username/repo-name` (no `https://github.com/` prefix in the JSON config)
- Check your internet connection

**No vulnerabilities found:**
- Lower the `vulnerability_threshold` value (try `1` or `2`)
- Verify the SonarQube project has completed analysis
- Check that the correct `sonar_project_key` is configured

**Mock SAST is running instead of real SonarQube:**
- This means SonarQube credentials or URL are missing or invalid
- Configure SonarQube settings via `cd sonar` in the interactive app
- Or update `sonar_url`, `sonar_username`, `sonar_password`, and `sonar_project_key` in `appsec_config.json`

**Output files not found:**
- When running as an EXE, reports are saved to a `CazeAppSecReport/` folder next to the executable
- Check the terminal output for the exact output path after the scan completes

---

## 3. DAST Security Analysis

### 3.1 Getting Started

DAST (Dynamic Application Security Testing) tests a running web application by sending real HTTP requests and analyzing responses. AppSecAI uses OWASP ZAP as its DAST engine. ZAP performs spider crawling, passive scanning, and active attack simulation against your target URL.

**What you need before starting:**

| Requirement | Details |
|-------------|----------|
| AppSecAI.exe | Present in a writable folder on your machine |
| OWASP ZAP | Installed on your machine (version 2.x or later) |
| Target web application | A running web app accessible via URL |
| Target URL | The full URL of the application to scan (e.g., `https://app.example.com`) |
| Write permissions | AppSecAI needs to write reports to the output directory |

**Install OWASP ZAP:**

Download ZAP from https://www.zaproxy.org/download/ and install it. AppSecAI will auto-detect the ZAP installation path on Windows. If auto-detection fails, you can specify the path manually in the configuration.

> Note: The bundled ZAP version in the `external/ZAP_2.16.1/` folder is used automatically if a system-wide ZAP installation is not found.

---

### 3.2 Navigation Basics

From the interactive app, navigate to DAST settings using:

| Command | Action |
|---------|--------|
| `cd settings` | Go to Settings Menu |
| `cd dast` | Go to DAST Settings |
| `cd scan` | Go to Security Analysis menu |
| `cd ..` | Go back one level |
| `cd /` | Go to Main Menu |
| `cd/list` | Show available menus from current location |

---

### 3.3 DAST - Settings and Configuration

DAST requires a target URL and optionally a ZAP installation path. You can configure these in either mode.

#### Mode 1: JSON Config File

Open `appsec_config.json` and fill in the DAST-relevant fields:

```json
{
  "dast_url": "https://your-target-app.com",
  "zap_report_path": "",
  "vulnerability_threshold": 3,
  "output_dir": "AppSecAI_output"
}
```

The `app_config.yaml` file contains additional ZAP settings that can be tuned:

```yaml
security_tools:
  zap:
    enabled: true
    installation_path: ''   # Leave empty for auto-detection
    scan_policy: Default Policy
    max_scan_time: 3600     # Maximum scan duration in seconds
    spider_max_depth: 5     # How deep the spider crawls
  owasp_zap:
    target_url: https://your-target-app.com
    enable_passive: true    # Run passive scan
    enable_active: true     # Run active attack scan
    enable_spider: true     # Crawl the application
    enable_api_scan: false  # Enable for REST API targets
    use_auth: false         # Enable if authentication is required
```

Save both files, then launch `AppSecAI.exe` and press Enter to use Mode 1.

#### Mode 2: Interactive CLI

1. Launch `AppSecAI.exe` and select Mode 2
2. Complete the initial setup wizard (GitHub credentials and general settings)
3. Navigate to DAST settings:

```
cd settings
cd dast
```

The DAST Settings menu appears:

```
DAST Settings
Current Location: Main Menu > Settings Menu > DAST Settings

1. Configure Target URL
2. Configure ZAP Installation Path
3. Configure Scan Policy
4. Configure Max Scan Time
5. Configure Spider Max Depth
6. Show Current Settings
0. Back
```

**Option 1 - Configure Target URL:** Enter the full URL of the web application to scan (e.g., `https://app.example.com`). The URL must include the protocol (`http://` or `https://`).

**Option 2 - Configure ZAP Installation Path:** Enter the full path to your ZAP installation directory. Leave blank to use auto-detection. Example: `C:\Program Files\OWASP\Zed Attack Proxy`

**Option 3 - Configure Scan Policy:** Enter the ZAP scan policy name. Default is `Default Policy`. Custom policies can be created in the ZAP GUI.

**Option 4 - Configure Max Scan Time:** Enter the maximum time in seconds for the DAST scan. Default is `3600` (1 hour). For large applications, increase this value.

**Option 5 - Configure Spider Max Depth:** Enter the maximum crawl depth for the ZAP spider. Default is `5`. Higher values find more pages but take longer.

**Option 6 - Show Current Settings:** Displays all currently configured DAST settings.

---

### 3.4 Saving Your Configuration

#### In JSON Config Mode

All changes made to `appsec_config.json` are saved immediately when you save the file. The app reads the file fresh each time you launch it.

#### In Interactive CLI Mode

Settings entered during the interactive wizard are stored as environment variables for the current session. To persist them for future sessions:

1. After completing the wizard, when prompted:

```
Save these settings to .env file for future use? (Y/n):
```

Press Enter (or type `Y`) to save.

2. The `.env` file is created in the same folder as `AppSecAI.exe` and loaded automatically on next launch.

**Configuration priority (highest to lowest):**

1. Active environment variables (set during current session)
2. `appsec_config.json` (manual file edits)
3. `.env` file (saved wizard session)
4. `app_config.yaml` (system defaults)

---

### 3.5 Running a DAST Scan

#### Via JSON Config Mode

1. Ensure `appsec_config.json` has `dast_url` set to your target URL
2. Launch `AppSecAI.exe` and press Enter to select Mode 1
3. From the main menu, select **2. Run DAST Scan**
4. The pipeline runs automatically:
   - Locates the ZAP installation
   - Launches ZAP in headless mode
   - Runs the spider to crawl the application
   - Performs passive scanning
   - Performs active attack scanning
   - Generates a ZAP HTML report
   - Applies AI-powered risk scoring to findings
   - Filters results by your threshold
   - Saves output files to `AppSecAI_output/`

#### Via Interactive CLI Mode

1. Launch `AppSecAI.exe` and select Mode 2
2. Complete the setup wizard and configure DAST settings (Section 3.3)
3. From the scan menu, type `2` or `cd dast` to run the DAST scan

#### Via Direct CLI Command

```
AppSecAI.exe scan --type dast --target https://your-target-app.com
```

With additional options:

```
AppSecAI.exe scan --type dast --target https://your-target-app.com --threshold 3 --timeout 1800 --export --output-dir dast_results
```

**All `scan` flags for DAST:**

| Flag | Description |
|------|-------------|
| `--type dast` | Run DAST scan |
| `--target` | Target web application URL (required) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--timeout` | Scan timeout in seconds (overrides config) |
| `--export` | Export results to files |
| `--output-dir` | Output directory (overrides config) |
| `--auto-fix` | Automatically run AI remediation after scan |
| `--interactive-pr` | Ask for confirmation before creating PRs (use with `--auto-fix`) |

**Combined SAST + DAST scan:**

```
AppSecAI.exe scan --type both --target https://github.com/username/repo.git --dast-url https://your-target-app.com
```

**What happens during the scan:**

1. ZAP installation is located (auto-detected or from config)
2. ZAP launches in headless/daemon mode
3. Spider crawls the target URL up to the configured depth
4. Passive scan analyzes all traffic observed during crawling
5. Active scan sends attack payloads to discovered endpoints
6. ZAP generates an HTML report
7. AppSecAI parses the report and normalizes findings
8. AI-powered risk scoring is applied to each finding
9. Findings below the threshold are filtered out
10. Results are saved to the output directory

**DAST output files:**

| File | Description |
|------|-------------|
| `zap_report_<timestamp>.html` | Full ZAP HTML report |
| `dast_vulnerabilities_<timestamp>.csv` | Normalized vulnerability list |
| `dast_report_<timestamp>.json` | JSON vulnerability data (when `--export` is used) |

**Key DAST vulnerability fields:**

| Field | Description |
|-------|-------------|
| `id` | Unique vulnerability identifier |
| `severity` | Severity level: `Critical`, `High`, `Medium`, `Low` |
| `title` | Vulnerability name (e.g., `SQL Injection`, `XSS`) |
| `url` | Affected URL |
| `parameter` | Affected parameter or input field |
| `risk_level` | ZAP risk level |
| `confidence` | ZAP confidence level |
| `solution` | Recommended fix |
| `cwe_id` | CWE identifier |
| `risk_score` | AI-computed risk score |
| `ai_recommendation` | AI-generated remediation advice |

---

### 3.6 Quick Reference - Navigation Commands

| Command | Action |
|---------|--------|
| `cd settings` | Open Settings Menu |
| `cd dast` | Open DAST Settings |
| `cd threshold` | Open Vulnerability Thresholds |
| `cd scan` | Open Security Analysis menu |
| `cd output` | Configure Output Directory |
| `cd ..` | Go back one level |
| `cd /` | Return to Main Menu |
| `cd/list` | Show available menus |
| `cd/help` | Show navigation help |

---

## 4. SCA Security Analysis

### 4.1 Getting Started

SCA (Software Composition Analysis) identifies known vulnerabilities in your third-party dependencies and open-source libraries. AppSecAI uses Trivy as its SCA engine. Trivy scans your project and produces a JSON report, which AppSecAI then ingests, normalizes, and re-prioritizes using its AI-powered risk scoring framework.

**SCA workflow overview:**

1. Run Trivy against your project to generate a JSON report
2. Provide the Trivy JSON report path to AppSecAI
3. AppSecAI analyzes the report, applies context-aware risk scoring, and produces prioritized output

**What you need before starting:**

| Requirement | Details |
|-------------|----------|
| AppSecAI.exe | Present in a writable folder on your machine |
| Trivy | Installed on your machine |
| Project to scan | A local directory, container image, or repository |
| Trivy JSON report | Generated by running Trivy against your target |

**Install Trivy:**

Download Trivy from https://github.com/aquasecurity/trivy/releases and add it to your system PATH. Verify installation:

```
trivy --version
```

---

### 4.2 Navigation Basics

From the interactive app, navigate to SCA settings using:

| Command | Action |
|---------|--------|
| `cd settings` | Go to Settings Menu |
| `cd sca` | Go to SCA Configuration |
| `cd scan` | Go to Security Analysis menu |
| `cd ..` | Go back one level |
| `cd /` | Go to Main Menu |
| `cd/list` | Show available menus from current location |

---

### 4.3 SCA - Settings and Configuration

SCA requires a Trivy JSON report as input. You can also configure the SCA context in `appsec_config.json` to improve risk scoring accuracy.

#### Step 1: Generate a Trivy Report

Run Trivy against your target and save the output as JSON. Choose the target type that matches your project:

**Filesystem scan (local project directory):**

```
trivy fs --format json --output trivy_report.json ./my-project
```

**Container image scan:**

```
trivy image --format json --output trivy_report.json my-image:latest
```

**Repository scan (remote Git repo):**

```
trivy repo --format json --output trivy_report.json https://github.com/username/repo
```

**Kubernetes cluster scan:**

```
trivy k8s --format json --output trivy_report.json --report all
```

The `trivy_report.json` file is what you will provide to AppSecAI.

#### Step 2: Configure SCA Settings

**Mode 1: JSON Config File**

Open `appsec_config.json` and fill in the SCA-relevant fields:

```json
{
  "sca_target_type": "fs",
  "sca_target_path": "./my-project",
  "vulnerability_threshold": 3,
  "output_dir": "AppSecAI_output"
}
```

`sca_target_type` options:

| Value | Description |
|-------|-------------|
| `fs` | Local filesystem directory |
| `image` | Container image |
| `repo` | Remote Git repository |
| `k8s` | Kubernetes cluster |
| `vm` | Virtual machine image |
| `rootfs` | Root filesystem |

**Mode 2: Interactive CLI**

1. Launch `AppSecAI.exe` and select Mode 2
2. Complete the initial setup wizard
3. Navigate to SCA settings:

```
cd settings
cd sca
```

The SCA Configuration menu appears:

```
SCA Configuration
Current Location: Main Menu > Settings Menu > SCA Configuration

1. Configure SCA Target Type
2. Configure SCA Target Path
3. Show Current Settings
0. Back
```

**Option 1 - Configure SCA Target Type:** Select the type of target Trivy will scan (`fs`, `image`, `repo`, `k8s`, `vm`, `rootfs`).

**Option 2 - Configure SCA Target Path:** Enter the path or URL of the target to scan.

#### Step 3: Configure SCA Context (Optional but Recommended)

The SCA context in `appsec_config.json` provides AppSecAI with information about your project environment, which improves the accuracy of AI-powered risk scoring. Navigate to it in the interactive app:

```
cd settings
cd sca context
```

The SCA Context Settings menu appears:

```
SCA Context Settings

1. Dependency Management
2. Package Sources
3. Vulnerability Response
4. Build Pipeline
5. Runtime Behavior
6. Ecosystem
7. Compliance
0. Back
```

Each sub-menu lets you configure context that influences how vulnerabilities are scored:

**1. Dependency Management** - Configure how your project manages dependencies:
- Update frequency (daily, weekly, monthly)
- Whether lock files are enforced
- Whether automated dependency updates are enabled
- Dependency pinning strategy (exact, minor, major)
- Whether SBOM generation is enabled

**2. Package Sources** - Configure your package registry settings:
- Whether a private registry is used
- Whether package signature verification is enabled
- Whether only trusted sources are allowed

**3. Vulnerability Response** - Configure your vulnerability management process:
- Mean time to patch (e.g., `< 7d`, `7-30d`, `> 30d`)
- Vulnerability monitoring frequency
- Whether an emergency patch process exists

**4. Build Pipeline** - Configure your CI/CD pipeline security:
- Whether dependency caching is used
- Whether builds are reproducible
- Whether dependency hash verification is enabled
- SLSA level (e.g., `slsa1`, `slsa2`, `slsa3`)

**5. Runtime Behavior** - Configure runtime dependency controls:
- Whether dependency isolation is enabled
- Whether sandboxing is enabled
- Whether dynamic loading is restricted

**6. Ecosystem** - Configure your project ecosystem:
- Primary language (e.g., `javascript`, `python`, `java`)
- Package manager (e.g., `npm`, `pip`, `maven`)
- Whether it is a monorepo

**7. Compliance** - Configure compliance requirements:
- SOC2, HIPAA, PCI-DSS, GDPR, ISO 27001 requirements

These settings are stored in the `AppSecAI.sca_context` section of `appsec_config.json`.

---

### 4.4 Saving Your Configuration

#### In JSON Config Mode

All changes to `appsec_config.json` are saved when you save the file. The app reads it fresh on each launch.

#### In Interactive CLI Mode

Settings entered interactively are stored as environment variables for the current session. To persist them:

1. When prompted at the end of the wizard:

```
Save these settings to .env file for future use? (Y/n):
```

Press Enter (or type `Y`) to save.

2. The `.env` file is created next to `AppSecAI.exe` and loaded automatically on next launch.

Alternatively, you can manually edit `appsec_config.json` to persist SCA context settings. The JSON config takes priority over the `.env` file.

---

### 4.5 Running an SCA Scan

#### Via JSON Config Mode

1. Generate a Trivy JSON report (see Section 4.3, Step 1)
2. Ensure `appsec_config.json` has `sca_target_type` and `sca_target_path` set
3. Launch `AppSecAI.exe` and press Enter to select Mode 1
4. From the main menu, select **3. Run SCA Scan**
5. When prompted, enter the path to your Trivy JSON report
6. AppSecAI processes the report:
   - Reads and parses the Trivy JSON report
   - Normalizes vulnerabilities, misconfigurations, and secrets
   - Extracts CVSS scores and CWE identifiers
   - Applies AI-powered risk scoring using your SCA context
   - Filters results by your threshold
   - Saves prioritized output to `AppSecAI_output/`

#### Via Interactive CLI Mode

1. Launch `AppSecAI.exe` and select Mode 2
2. Complete the setup wizard and configure SCA settings (Section 4.3)
3. From the scan menu, type `3` or `cd sca` to run the SCA scan
4. When prompted, enter the path to your Trivy JSON report

#### Via Direct CLI Command (scan subcommand)

```
AppSecAI.exe scan --type sca --target ./my-project
```

With additional options:

```
AppSecAI.exe scan --type sca --target ./my-project --target-type fs --threshold 3 --export --output-dir sca_results
```

#### Via Direct CLI Command (sca subcommand - for existing Trivy reports)

If you already have a Trivy JSON report, use the `sca` subcommand directly:

```
AppSecAI.exe sca --trivy-report trivy_report.json
```

With export options:

```
AppSecAI.exe sca --trivy-report trivy_report.json --threshold 3 --output-dir sca_results --export-json --export-pdf
```

**All `sca` subcommand flags:**

| Flag | Description |
|------|-------------|
| `--trivy-report` | Path to Trivy JSON report file (required) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--output-dir` | Output directory for SCA results (overrides config) |
| `--export-json` | Export prioritized SCA findings to JSON |
| `--export-pdf` | Generate SCA security posture PDF report |

**All `scan --type sca` flags:**

| Flag | Description |
|------|-------------|
| `--type sca` | Run SCA scan |
| `--target` | Target path or URL to scan |
| `--target-type` | Target type: `fs`, `image`, `repo`, `k8s`, `vm`, `rootfs` (default: `fs`) |
| `--threshold` | Vulnerability score threshold (overrides config) |
| `--export` | Export results to files |
| `--output-dir` | Output directory (overrides config) |

**What AppSecAI analyzes in the Trivy report:**

| Finding Type | Description |
|-------------|-------------|
| Vulnerabilities | Known CVEs in package dependencies |
| Misconfigurations | Infrastructure and configuration issues |
| Secrets | Exposed credentials, API keys, tokens |

**SCA output files:**

| File | Description |
|------|-------------|
| `sca_prioritized_<timestamp>.csv` | Prioritized vulnerability list |
| `sca_findings_<timestamp>.json` | JSON findings (when `--export-json` is used) |
| `sca_report_<timestamp>.pdf` | PDF security posture report (when `--export-pdf` is used) |

**Key SCA vulnerability fields:**

| Field | Description |
|-------|-------------|
| `id` | Unique vulnerability identifier |
| `source_id` | CVE or finding ID from Trivy |
| `scanner` | Scanner that found the issue (e.g., `trivy`) |
| `target` | File or component where the issue was found |
| `severity` | Severity level: `Critical`, `High`, `Medium`, `Low` |
| `package_name` | Affected package name |
| `installed_version` | Currently installed version |
| `fixed_version` | Version that fixes the vulnerability |
| `cvss_score` | CVSS base score |
| `cwe_id` | CWE identifier |
| `risk_score` | AI-computed risk score (higher = more critical) |
| `risk_level` | Risk level: `Critical`, `High`, `Medium`, `Low`, `Info` |
| `category` | Vulnerability category (e.g., `injection`, `crypto`, `auth`) |
| `ai_justification` | AI-generated explanation of why this is prioritized |

**Terminal summary example:**

```
[PRIORITIZATION] Processing 142 vulnerabilities with threshold: 3.0
SCA Analysis Complete
   Scan ID: sca_20260508_151234
   Total findings in report: 142
   After threshold filtering (score >= 3.0): 38
   Critical: 6  |  High: 18  |  Medium: 12  |  Low: 2
   Output: AppSecAI_output/sca_prioritized_20260508_151234.csv
```

---

### 4.6 Quick Reference - Navigation Commands

| Command | Action |
|---------|--------|
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
| `cd/help` | Show navigation help |

---

