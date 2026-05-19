---
name: "android-build-env-setup"
description: "Sets up Android app build environment without Android Studio. Invoke when user needs to build Android apps without installing Android Studio, or asks about Android CLI build setup."
---

# Android Build Environment Setup (Without Android Studio)

This skill guides you through setting up a complete Android application build environment without installing Android Studio. It prioritizes using the **Android CLI** (Google's official terminal-based tool) to dramatically simplify setup, with manual fallback steps when needed.

## Overview

The recommended approach uses **Android CLI** as the primary interface. It reduces LLM token usage by 70% and completes tasks 3x faster than manual SDK tooling.

**Two setup paths:**
1. **Android CLI Path (Recommended)** — Use `android` CLI commands for SDK management, project creation, emulator management, and deployment
2. **Manual Path (Fallback)** — Use traditional sdkmanager/Gradle commands when Android CLI is not available

## Step-by-Step Setup

### Step 0: Check Existing Environment (REQUIRED FIRST)

**IMPORTANT: You MUST check for existing tools before installing anything. Skip installation if tools are already available.**

#### Check Android CLI (Primary Check)

First, check if Android CLI is installed:
```bash
android --version
```

**If Android CLI is available:**
- Run `android info` to check SDK location, JDK, and environment status
- If SDK and JDK are properly configured, skip to Step 8 (Build an Android Project)
- If SDK components are missing, proceed to Step 3 (Install SDK via Android CLI)

**If Android CLI is NOT installed:**
- Proceed to Step 1 to install prerequisites, then install Android CLI

#### Check JDK
- Verify if Java is installed (check `java -version`)
- Verify if JDK compiler is available (check `javac -version`)
- Check if JAVA_HOME environment variable is set

**If JDK 11 or 17 is already installed and JAVA_HOME is set, SKIP Step 1.**

#### Check Android SDK (Manual Path Only)
- Check if ANDROID_HOME environment variable is set
- Verify if sdkmanager command is available
- Verify if adb command is available
- Check if build-tools are installed in the SDK directory

**If Android SDK is already set up with required components, SKIP Steps 4-6.**

#### Decision Matrix

| Check Result | Action |
|--------------|--------|
| Android CLI installed, SDK ready | ✓ Skip to Step 8 |
| Android CLI installed, SDK missing | → Step 3 (Install SDK via CLI) |
| Android CLI NOT installed | → Step 1 (Install JDK) → Step 2 (Install Android CLI) |
| JDK installed, JAVA_HOME set | ✓ Skip Step 1 |
| JDK NOT installed | → Proceed to Step 1 |
| ANDROID_HOME set, tools exist (manual path) | ✓ Skip Steps 4-6 |
| ANDROID_HOME NOT set (manual path) | → Proceed to Steps 4-6 |

**After completing environment check, proceed only with the steps that are actually needed.**

### Step 1: Install JDK

Install JDK 11 or JDK 17 based on the operating system:

- **Linux (Debian/Ubuntu)**: Install openjdk-17-jdk using package manager
- **Linux (Fedora/RHEL)**: Install java-17-openjdk-devel using dnf
- **macOS**: Install openjdk@17 using Homebrew
- **Windows**: Install openjdk17 using Chocolatey, or manually download from adoptium.net

**Verify installation** by checking `java -version` and `javac -version`.

### Step 2: Set JAVA_HOME and Install Android CLI

#### Set JAVA_HOME Environment Variable

Configure JAVA_HOME environment variable:

- **Linux/macOS**: Add JAVA_HOME to shell configuration file (~/.bashrc or ~/.zshrc)
- **Windows**: Set JAVA_HOME using PowerShell or System Environment Variables

**Important:** Restart terminal or reload shell configuration after setting environment variables.

#### Install Android CLI

Download Android CLI from the official page: https://d.android.com/tools/agents

Available for Apple Silicon (macOS), AMD64 Linux, and AMD64 Windows.

**⚠️ IMPORTANT: PATH Priority Conflict with Existing Android SDK**

If you already have the Android SDK installed, the old SDK `tools/bin/android` command may shadow the new Android CLI `android` command. The system resolves commands by searching PATH directories in order — whichever comes first wins.

**You MUST ensure the Android CLI path is placed BEFORE the Android SDK tools path in your PATH.**

Verify which `android` command is being used:
```bash
which android
```

If this points to something like `$ANDROID_HOME/tools/bin/android` instead of the Android CLI binary, your PATH is misconfigured.

**How to fix:**

- **Linux/macOS**: In your shell config (~/.bashrc or ~/.zshrc), add the Android CLI path BEFORE the SDK tools path:
  ```bash
  export PATH="/path/to/android-cli:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"
  ```
  Do NOT simply append with `export PATH="$PATH:/path/to/android-cli"` — this puts it at the end and the old SDK command takes priority.

- **Windows**: In System Environment Variables, move the Android CLI entry above the Android SDK tools entry in the PATH list.

After installation, verify the correct `android` is being used:
```bash
android --version
which android
```

Keep Android CLI up to date:
```bash
android update
```

### Step 3: Install SDK Components via Android CLI (Recommended)

**If Android CLI is installed, use this step instead of Steps 4-6.**

Use `android sdk install` to download only the specific components needed:

```bash
android sdk install platforms/android-35 build-tools/35.0.0 platform-tools
```

This replaces the traditional manual process of downloading command-line tools, setting ANDROID_HOME, and running sdkmanager individually.

**Key advantages over manual setup:**
- No need to manually download and extract command-line tools
- No need to set ANDROID_HOME manually (Android CLI manages it)
- No need to organize directory structures
- Lean environment — only installs what you need

**Other SDK management commands:**
```bash
android sdk list --all
android sdk update
android sdk remove <pkg-name>
```

**Verify installation:**
```bash
android info
```

**If Android CLI is NOT available**, proceed to Steps 4-6 (Manual Path).

### Step 4: Download Android SDK Command-line Tools (Manual Path)

**IMPORTANT: Use China Mirror for faster downloads in China**

#### China Mirror Sources (Recommended for users in China)

| Resource | Mirror URL |
|----------|------------|
| Android SDK (Tsinghua) | https://mirrors.tuna.tsinghua.edu.cn/android/repository/ |
| Android SDK (USTC) | https://mirrors.ustc.edu.cn/android/repository/ |

Download the appropriate command-line tools package for the operating system:
- **Linux**: commandlinetools-linux
- **macOS**: commandlinetools-mac
- **Windows**: commandlinetools-win

Extract the downloaded package and organize the directory structure properly. The cmdline-tools directory should be renamed to "latest" inside the SDK directory.

### Step 5: Set ANDROID_HOME Environment Variable (Manual Path)

Configure ANDROID_HOME environment variable:

- **Linux**: Set ANDROID_HOME to ~/Android/Sdk
- **macOS**: Set ANDROID_HOME to ~/Library/Android/sdk
- **Windows**: Set ANDROID_HOME to %LOCALAPPDATA%\Android\Sdk

Add the following paths to PATH:
- ANDROID_HOME/cmdline-tools/latest/bin
- ANDROID_HOME/platform-tools
- ANDROID_HOME/emulator

**Important:** Restart terminal or reload shell configuration after setting environment variables.

### Step 6: Install Required SDK Components (Manual Path)

Use sdkmanager to install required components:

1. **Accept licenses** - Run sdkmanager with --licenses flag and accept all licenses
2. **Install build-tools** - Install the required build-tools version (e.g., 34.0.0)
3. **Install platform-tools** - Includes adb and other essential tools
4. **Install Android platform** - Install the target SDK version (e.g., android-34)
5. **Optional: Install emulator** - If emulator is needed for testing

### Step 7: Configure Gradle Mirror (REQUIRED for China)

**CRITICAL: You MUST configure Gradle to use China mirror before running any Gradle commands. Gradle downloads are extremely slow without mirror configuration.**

When gradlew is first executed, it will download the Gradle distribution. This download is very slow in China. You MUST modify the download URL to use a mirror.

#### Method 1: Modify gradle-wrapper.properties (RECOMMENDED)

Edit the `gradle/wrapper/gradle-wrapper.properties` file in the project and change the distributionUrl to use a mirror:

**Gradle Mirror URLs:**
| Mirror | URL |
|--------|-----|
| Tencent Cloud | https://mirrors.cloud.tencent.com/gradle/ |
| Aliyun | https://mirrors.aliyun.com/gradle/ |
| Huawei Cloud | https://repo.huaweicloud.com/gradle/ |

#### Method 2: Configure Gradle Repository Mirror (for dependencies)

Create or edit `~/.gradle/init.gradle` to configure Maven repository mirrors for faster dependency downloads.

**Maven Repository Mirrors:**
| Mirror | URL |
|--------|-----|
| Aliyun Google | https://maven.aliyun.com/repository/google |
| Aliyun Public | https://maven.aliyun.com/repository/public |
| Aliyun JCenter | https://maven.aliyun.com/repository/jcenter |
| Aliyun Gradle Plugin | https://maven.aliyun.com/repository/gradle-plugin |
| Huawei Maven | https://repo.huaweicloud.com/repository/maven/ |

### Step 8: Create an Android Project

#### Option A: Using Android CLI (Recommended)

Create a new project from official templates with recommended architecture applied from the start:

```bash
android create empty-activity --name="My App" --output=./my-app
```

**List available templates:**
```bash
android create --list
```

**Create with specific options:**
```bash
android create compose-empty --name="My App" --package com.example.myapp --minSdk=24 --output=./my-app
```

This produces a modern Android project with Jetpack Compose, Material 3, and a sensible default structure. No manual Gradle configuration is required.

**⚠️ IMPORTANT: Always Use Gradle Wrapper (gradlew) to Build**

You MUST use `./gradlew` (Linux/macOS) or `gradlew.bat` (Windows) to build Android projects — **NEVER use the global `gradle` command**.

**Why gradlew is mandatory:**
1. **Version consistency** — Each project specifies its exact Gradle version in `gradle/wrapper/gradle-wrapper.properties`. Using global `gradle` may cause version mismatch errors.
2. **No manual installation** — `gradlew` auto-downloads the correct Gradle version on first run. No need to install Gradle globally.
3. **Reproducible builds** — All team members and CI servers use the same Gradle version, ensuring consistent build results.
4. **Android Gradle Plugin compatibility** — AGP versions require specific Gradle versions. `gradlew` guarantees the right pairing.

**If global Gradle is installed on the system, remove it to avoid conflicts:**
```bash
gradle --version
```
If this returns a version, consider uninstalling it to prevent accidental use.

**Correct usage:**
```bash
./gradlew assembleDebug
```

**Incorrect usage (NEVER do this):**
```bash
gradle assembleDebug
```

#### Option B: Manual Project Setup

If you have an existing project, navigate to the project directory and use Gradle Wrapper to build:
- Use `./gradlew` on Linux/macOS
- Use `gradlew.bat` on Windows

Common build tasks:
- Clean build: `./gradlew clean`
- Build debug APK: `./gradlew assembleDebug`
- Build release APK: `./gradlew assembleRelease`
- Install on connected device: `./gradlew installDebug`

### Step 9: Set Up Emulator and Run App

#### Option A: Using Android CLI (Recommended)

Create a virtual device:
```bash
android emulator create
```

Start the emulator:
```bash
android emulator start
```

List available virtual devices:
```bash
android emulator list
```

Build and deploy the app:
```bash
./gradlew assembleDebug
android run --apks app/build/outputs/apk/debug/app-debug.apk
```

Or run in debug mode:
```bash
android run --apks app/build/outputs/apk/debug/app-debug.apk --debug
```

Stop the emulator when done:
```bash
android emulator stop
```

#### Option B: Manual Emulator Setup

Use the traditional AVD Manager and adb commands to create and manage virtual devices.

### Step 10: Install Android Skills and Use Knowledge Base (Optional)

Android Skills are modular, markdown-based instruction sets that help agents execute specific patterns following best practices.

#### Install Skills

Browse available skills:
```bash
android skills list
```

Install a specific skill:
```bash
android skills add --skill=r8-analyzer --project=.
```

Install all Android skills:
```bash
android skills add --all
```

Find skills by keyword:
```bash
android skills find <keyword>
```

**Available skills include:**
- Navigation 3 setup and migration
- Edge-to-edge UI support
- AGP 9 upgrade and XML-to-Compose migration
- R8 configuration analysis
- Camera1 to CameraX migration
- Testing setup

#### Query Android Documentation

Search authoritative Android developer documentation:
```bash
android docs search <keywords>
```

Fetch specific documentation:
```bash
android docs fetch <topic>
```

This ensures agents have access to the latest guidance even if their training data is outdated.

### Step 11: Inspect and Debug (Optional)

#### UI Layout Inspection

Inspect the UI layout of a running app (faster than screenshots for debugging):
```bash
android layout --pretty
```

#### Screenshot Capture

Capture the current screen of a connected device:
```bash
android screen capture -o screenshot.png
```

#### Project Description

Analyze an Android project to generate descriptive metadata:
```bash
android describe --project_dir=.
```

## Common Issues and Solutions

### Issue 1: "android" command runs old SDK tool instead of Android CLI

This is the most common issue when Android SDK is already installed. The old `android` command from `$ANDROID_HOME/tools/bin/` takes priority over the new Android CLI.

**Diagnosis:**
```bash
which android
android --version
```

If `which android` shows a path under ANDROID_HOME (e.g., `$ANDROID_HOME/tools/bin/android`) instead of the Android CLI binary, the PATH order is wrong.

**Solution:** Ensure the Android CLI path appears BEFORE the Android SDK tools path in your PATH. See Step 2 for detailed instructions.

### Issue 2: "android: command not found"
- Ensure Android CLI is properly installed and in PATH
- Restart terminal after installation
- Run `android update` to verify connectivity

### Issue 2: "sdkmanager: command not found" (Manual Path)
- Ensure ANDROID_HOME/cmdline-tools/latest/bin is in PATH
- Restart terminal after setting environment variables

### Issue 3: "Java version mismatch"
- Android Gradle Plugin 8.x requires JDK 17
- Check java version and update JAVA_HOME accordingly

### Issue 4: "SDK location not found"
- Run `android info` to check SDK location (Android CLI path)
- Create or update local.properties in project root with sdk.dir path (Manual path)

### Issue 5: License not accepted
- Android CLI path: `android sdk install` handles licenses automatically
- Manual path: Run sdkmanager --licenses and accept all licenses

### Issue 6: Gradle permission denied (Linux/macOS)
- Make gradlew executable with chmod +x

### Issue 7: Android CLI update needed
- Run `android update` to get the latest version

## Platform-Specific Notes

### Linux
- May need 32-bit libraries for some tools
- For emulator, may need libvulkan1 library

### macOS
- On Apple Silicon (M1/M2/M3/M4), use ARM64 versions when available
- May need to allow SDK tools in Security settings

### Windows
- Run PowerShell as Administrator for environment variable changes
- Some antivirus software may block SDK downloads

## Quick Reference Commands

### Android CLI Commands (Recommended)

| Task | Command |
|------|---------|
| Check environment | `android info` |
| Install SDK packages | `android sdk install platforms/android-35 build-tools/35.0.0` |
| List SDK packages | `android sdk list --all` |
| Update SDK packages | `android sdk update` |
| Create project | `android create empty-activity --name="My App" --output=./my-app` |
| List templates | `android create --list` |
| Create emulator | `android emulator create` |
| Start emulator | `android emulator start` |
| List emulators | `android emulator list` |
| Run app | `android run --apks <apk-path>` |
| Capture screenshot | `android screen capture -o screenshot.png` |
| Inspect UI layout | `android layout --pretty` |
| List skills | `android skills list` |
| Install skill | `android skills add --skill=<name> --project=.` |
| Search docs | `android docs search <keywords>` |
| Update CLI | `android update` |
| Describe project | `android describe --project_dir=.` |

### Manual Commands (Fallback)

| Task | Command |
|------|---------|
| List installed packages | sdkmanager --list_installed |
| Update all packages | sdkmanager --update |
| Check connected devices | adb devices |
| View Gradle tasks | ./gradlew tasks |
| Build and install | ./gradlew installDebug |

## China Mirror Quick Reference

| Resource | Mirror URL |
|----------|------------|
| Android SDK (Tsinghua) | https://mirrors.tuna.tsinghua.edu.cn/android/repository/ |
| Android SDK (USTC) | https://mirrors.ustc.edu.cn/android/repository/ |
| Gradle (Tencent) | https://mirrors.cloud.tencent.com/gradle/ |
| Gradle (Aliyun) | https://mirrors.aliyun.com/gradle/ |
| Gradle (Huawei) | https://repo.huaweicloud.com/gradle/ |
| Maven (Aliyun) | https://maven.aliyun.com/repository/public |
| Maven Google (Aliyun) | https://maven.aliyun.com/repository/google |

## Minimal Setup for CI/CD

### Using Android CLI (Recommended)

1. Install JDK and set JAVA_HOME
2. Install Android CLI
3. Run `android sdk install platforms/android-35 build-tools/35.0.0 platform-tools`
4. Configure Gradle mirror in ~/.gradle/init.gradle (for China)

### Manual Setup

1. Set ANDROID_HOME and PATH environment variables
2. Download and extract Android SDK command-line tools (using China mirror)
3. Accept licenses and install required SDK components
4. Configure Gradle mirror in ~/.gradle/init.gradle

## Android CLI vs Manual Path: When to Use Which

| Task | Android CLI | Manual (sdkmanager) |
|------|-------------|---------------------|
| SDK setup in CI | ✅ Recommended | ⚠️ Verbose |
| Project creation from templates | ✅ | ❌ |
| Emulator management | ✅ | ⚠️ Complex |
| App deployment | ✅ `android run` | ⚠️ Manual adb |
| UI inspection | ✅ `android layout` | ❌ |
| Skills management | ✅ | ❌ |
| Doc searching | ✅ | ❌ |
| Existing project build | ✅ | ✅ |
| Fine-grained SDK control | ✅ | ✅ |

## Additional Resources

- Android CLI Download: https://d.android.com/tools/agents
- Android CLI Documentation: https://developer.android.com/tools/agents/android-cli
- Android Skills Repository: https://github.com/android/skills
- Android Skills Documentation: https://developer.android.com/tools/agents/android-skills
- Android SDK Command-line Tools: https://developer.android.com/studio#command-tools
- Gradle Plugin Release Notes: https://developer.android.com/build/releases/gradle-plugin
- Android Build Overview: https://developer.android.com/build

---

**Note:** Android CLI is the recommended approach for building Android applications from the command line. It dramatically simplifies setup and is designed for agentic workflows, CI/CD, and scripted automation. For visual UI design, interactive debugging, and advanced profiling, transition your project to Android Studio.
