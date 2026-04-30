---
name: "android-build-env-setup"
description: "Sets up Android app build environment without Android Studio. Invoke when user needs to build Android apps without installing Android Studio, or asks about Android CLI build setup."
---

# Android Build Environment Setup (Without Android Studio)

This skill guides you through setting up a complete Android application build environment without installing Android Studio. Supports Linux, Windows, and macOS platforms.

## Overview

Building Android apps requires the following components:
1. **JDK (Java Development Kit)** - Version 11 or 17 recommended
2. **Android SDK Command-line Tools** - Core SDK tools
3. **Android SDK Build Tools** - For compiling and packaging
4. **Android SDK Platform Tools** - ADB and other tools
5. **Android Platform** - Target SDK version
6. **Gradle** - Build automation (optional if using Gradle Wrapper)

## Step-by-Step Setup

### Step 0: Check Existing Environment (REQUIRED FIRST)

**IMPORTANT: You MUST check for existing tools before installing anything. Skip installation if tools are already available.**

Check the following components:

#### Check JDK
- Verify if Java is installed (check `java -version`)
- Verify if JDK compiler is available (check `javac -version`)
- Check if JAVA_HOME environment variable is set

**If JDK 11 or 17 is already installed and JAVA_HOME is set, SKIP Step 1.**

#### Check Android SDK
- Check if ANDROID_HOME environment variable is set
- Verify if sdkmanager command is available
- Verify if adb command is available
- Check if build-tools are installed in the SDK directory

**If Android SDK is already set up with required components, SKIP Steps 3-6.**

#### Check Gradle
- Check if Gradle is installed globally
- Check if Gradle Wrapper (gradlew) exists in the project directory

**⚠️ WARNING: Global Gradle Conflict Risk**

If global Gradle is installed on the system, it may conflict with the project's Gradle Wrapper (gradlew). This can cause:
- Version mismatch errors
- Inconsistent build behavior
- "Gradle version mismatch" issues

**RECOMMENDED ACTION:**
- **Remove global Gradle** to avoid conflicts
- Use **Gradle Wrapper (gradlew)** instead - it will automatically download the correct Gradle version for the project
- Gradle Wrapper ensures all team members use the same Gradle version

**Why Gradle Wrapper is better:**
1. **Version consistency** - Every developer uses the exact same Gradle version
2. **No manual installation** - gradlew auto-downloads the required version
3. **Project-specific** - Each project can use a different Gradle version
4. **CI/CD friendly** - Build servers don't need Gradle pre-installed

#### Decision Matrix

| Check Result | Action |
|--------------|--------|
| Java installed, JAVA_HOME set | ✓ Skip Step 1 |
| Java NOT installed | → Proceed to Step 1 |
| ANDROID_HOME set, tools exist | ✓ Skip Steps 3-6 |
| ANDROID_HOME NOT set | → Proceed to Steps 3-6 |
| Gradle Wrapper exists, no global Gradle | ✓ Perfect! Use gradlew |
| Gradle Wrapper exists + global Gradle | ⚠️ Remove global Gradle to avoid conflicts |
| Only global Gradle found | ⚠️ Remove global Gradle, use Gradle Wrapper |
| No Gradle found | ✓ Will use Gradle Wrapper (auto-download on first run) |

**After completing environment check, proceed only with the steps that are actually needed.**

### Step 1: Install JDK

Install JDK 11 or JDK 17 based on the operating system:

- **Linux (Debian/Ubuntu)**: Install openjdk-17-jdk using package manager
- **Linux (Fedora/RHEL)**: Install java-17-openjdk-devel using dnf
- **macOS**: Install openjdk@17 using Homebrew
- **Windows**: Install openjdk17 using Chocolatey, or manually download from adoptium.net

**Verify installation** by checking `java -version` and `javac -version`.

### Step 2: Set JAVA_HOME Environment Variable

Configure JAVA_HOME environment variable:

- **Linux/macOS**: Add JAVA_HOME to shell configuration file (~/.bashrc or ~/.zshrc)
- **Windows**: Set JAVA_HOME using PowerShell or System Environment Variables

**Important:** Restart terminal or reload shell configuration after setting environment variables.

### Step 3: Download Android SDK Command-line Tools

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

### Step 4: Set ANDROID_HOME Environment Variable

Configure ANDROID_HOME environment variable:

- **Linux**: Set ANDROID_HOME to ~/Android/Sdk
- **macOS**: Set ANDROID_HOME to ~/Library/Android/sdk
- **Windows**: Set ANDROID_HOME to %LOCALAPPDATA%\Android\Sdk

Add the following paths to PATH:
- ANDROID_HOME/cmdline-tools/latest/bin
- ANDROID_HOME/platform-tools
- ANDROID_HOME/emulator

**Important:** Restart terminal or reload shell configuration after setting environment variables.

### Step 5: Install Required SDK Components

Use sdkmanager to install required components:

1. **Accept licenses** - Run sdkmanager with --licenses flag and accept all licenses
2. **Install build-tools** - Install the required build-tools version (e.g., 34.0.0)
3. **Install platform-tools** - Includes adb and other essential tools
4. **Install Android platform** - Install the target SDK version (e.g., android-34)
5. **Optional: Install emulator** - If emulator is needed for testing

### Step 6: Verify Installation

Verify all components are properly installed:
- Check sdkmanager can list installed packages
- Check adb version
- Check build-tools directory exists

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

### Step 8: Build an Android Project

Navigate to the Android project directory and use Gradle Wrapper to build:
- Use `./gradlew` on Linux/macOS
- Use `gradlew.bat` on Windows

Common build tasks:
- Clean build
- Build debug APK (assembleDebug)
- Build release APK (assembleRelease)
- Install on connected device (installDebug)

## Common Issues and Solutions

### Issue 1: "sdkmanager: command not found"
- Ensure ANDROID_HOME/cmdline-tools/latest/bin is in PATH
- Restart terminal after setting environment variables

### Issue 2: "Java version mismatch"
- Android Gradle Plugin 8.x requires JDK 17
- Check java version and update JAVA_HOME accordingly

### Issue 3: "SDK location not found"
- Create or update local.properties in project root with sdk.dir path

### Issue 4: License not accepted
- Run sdkmanager --licenses and accept all licenses

### Issue 5: Gradle permission denied (Linux/macOS)
- Make gradlew executable with chmod +x

## Platform-Specific Notes

### Linux
- May need 32-bit libraries for some tools
- For emulator, may need libvulkan1 library

### macOS
- On Apple Silicon (M1/M2), use ARM64 versions when available
- May need to allow SDK tools in Security settings

### Windows
- Run PowerShell as Administrator for environment variable changes
- Some antivirus software may block SDK downloads

## Quick Reference Commands

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

For automated builds, the minimal setup includes:
1. Set ANDROID_HOME and PATH environment variables
2. Download and extract Android SDK command-line tools (using China mirror)
3. Accept licenses and install required SDK components
4. Configure Gradle mirror in ~/.gradle/init.gradle

## Additional Resources

- Android SDK Command-line Tools: https://developer.android.com/studio#command-tools
- Gradle Plugin Release Notes: https://developer.android.com/build/releases/gradle-plugin
- Android Build Overview: https://developer.android.com/build

---

**Note:** This setup provides all necessary tools for building Android applications from the command line. For debugging and advanced features, you may still want Android Studio, but it is not required for building APKs.
