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

Run the following checks to determine what needs to be installed:

#### Check JDK
```bash
# Check if Java is installed
java -version

# Check if JDK compiler is available
javac -version

# Check JAVA_HOME
echo $JAVA_HOME        # Linux/macOS
echo %JAVA_HOME%       # Windows CMD
$env:JAVA_HOME         # Windows PowerShell
```

**If JDK 11 or 17 is already installed and JAVA_HOME is set, SKIP Step 1.**

#### Check Android SDK
```bash
# Check if Android SDK exists
echo $ANDROID_HOME                     # Linux/macOS
echo %ANDROID_HOME%                    # Windows CMD
$env:ANDROID_HOME                      # Windows PowerShell

# Check if sdkmanager is available
sdkmanager --version

# Check if adb is available
adb version

# Check if build-tools are installed
ls $ANDROID_HOME/build-tools/          # Linux/macOS
dir %ANDROID_HOME%\build-tools\        # Windows
```

**If Android SDK is already set up with required components, SKIP Steps 3-6.**

#### Check Gradle
```bash
# Check if Gradle is installed globally
gradle -v

# Check if Gradle Wrapper exists in project
ls gradlew                             # Linux/macOS
dir gradlew.bat                        # Windows
```

**If Gradle Wrapper exists in your project, you don't need global Gradle installation.**

#### Quick Environment Check Script

**Linux/macOS:**
```bash
#!/bin/bash
echo "=== Environment Check ==="

echo -e "\n[1/6] Checking Java..."
if command -v java &> /dev/null; then
    java -version 2>&1 | head -n 1
    echo "✓ Java is installed"
else
    echo "✗ Java NOT found - Need to install"
fi

echo -e "\n[2/6] Checking JAVA_HOME..."
if [ -n "$JAVA_HOME" ]; then
    echo "✓ JAVA_HOME=$JAVA_HOME"
else
    echo "✗ JAVA_HOME NOT set"
fi

echo -e "\n[3/6] Checking Android SDK..."
if [ -n "$ANDROID_HOME" ]; then
    echo "✓ ANDROID_HOME=$ANDROID_HOME"
    if [ -d "$ANDROID_HOME/build-tools" ]; then
        echo "✓ Build tools installed: $(ls $ANDROID_HOME/build-tools/ | tr '\n' ' ')"
    else
        echo "✗ Build tools NOT found"
    fi
else
    echo "✗ ANDROID_HOME NOT set"
fi

echo -e "\n[4/6] Checking sdkmanager..."
if command -v sdkmanager &> /dev/null; then
    echo "✓ sdkmanager is available"
else
    echo "✗ sdkmanager NOT found"
fi

echo -e "\n[5/6] Checking adb..."
if command -v adb &> /dev/null; then
    adb version | head -n 1
    echo "✓ adb is available"
else
    echo "✗ adb NOT found"
fi

echo -e "\n[6/6] Checking Gradle..."
if [ -f "./gradlew" ]; then
    echo "✓ Gradle Wrapper found in current directory"
elif command -v gradle &> /dev/null; then
    gradle -v | head -n 1
    echo "✓ Gradle is installed globally"
else
    echo "✗ Gradle NOT found"
fi

echo -e "\n=== Check Complete ==="
```

**Windows (PowerShell):**
```powershell
Write-Host "=== Environment Check ===" -ForegroundColor Cyan

Write-Host "`n[1/6] Checking Java..." -ForegroundColor Yellow
if (Get-Command java -ErrorAction SilentlyContinue) {
    java -version
    Write-Host "✓ Java is installed" -ForegroundColor Green
} else {
    Write-Host "✗ Java NOT found - Need to install" -ForegroundColor Red
}

Write-Host "`n[2/6] Checking JAVA_HOME..." -ForegroundColor Yellow
if ($env:JAVA_HOME) {
    Write-Host "✓ JAVA_HOME=$env:JAVA_HOME" -ForegroundColor Green
} else {
    Write-Host "✗ JAVA_HOME NOT set" -ForegroundColor Red
}

Write-Host "`n[3/6] Checking Android SDK..." -ForegroundColor Yellow
if ($env:ANDROID_HOME) {
    Write-Host "✓ ANDROID_HOME=$env:ANDROID_HOME" -ForegroundColor Green
    if (Test-Path "$env:ANDROID_HOME\build-tools") {
        $tools = Get-ChildItem "$env:ANDROID_HOME\build-tools" -Name
        Write-Host "✓ Build tools installed: $tools" -ForegroundColor Green
    } else {
        Write-Host "✗ Build tools NOT found" -ForegroundColor Red
    }
} else {
    Write-Host "✗ ANDROID_HOME NOT set" -ForegroundColor Red
}

Write-Host "`n[4/6] Checking sdkmanager..." -ForegroundColor Yellow
if (Get-Command sdkmanager -ErrorAction SilentlyContinue) {
    Write-Host "✓ sdkmanager is available" -ForegroundColor Green
} else {
    Write-Host "✗ sdkmanager NOT found" -ForegroundColor Red
}

Write-Host "`n[5/6] Checking adb..." -ForegroundColor Yellow
if (Get-Command adb -ErrorAction SilentlyContinue) {
    adb version
    Write-Host "✓ adb is available" -ForegroundColor Green
} else {
    Write-Host "✗ adb NOT found" -ForegroundColor Red
}

Write-Host "`n[6/6] Checking Gradle..." -ForegroundColor Yellow
if (Test-Path ".\gradlew.bat") {
    Write-Host "✓ Gradle Wrapper found in current directory" -ForegroundColor Green
} elseif (Get-Command gradle -ErrorAction SilentlyContinue) {
    gradle -v
    Write-Host "✓ Gradle is installed globally" -ForegroundColor Green
} else {
    Write-Host "✗ Gradle NOT found" -ForegroundColor Red
}

Write-Host "`n=== Check Complete ===" -ForegroundColor Cyan
```

#### Decision Matrix

| Check Result | Action |
|--------------|--------|
| Java installed, JAVA_HOME set | ✓ Skip Step 1 |
| Java NOT installed | → Proceed to Step 1 |
| ANDROID_HOME set, tools exist | ✓ Skip Steps 3-6 |
| ANDROID_HOME NOT set | → Proceed to Steps 3-6 |
| Gradle Wrapper exists | ✓ No Gradle installation needed |
| Gradle NOT found | → Will use Gradle Wrapper (auto-download) |

**After completing environment check, proceed only with the steps that are actually needed.**

### Step 1: Install JDK

#### Linux (Debian/Ubuntu)
```bash
sudo apt update
sudo apt install openjdk-17-jdk
```

#### Linux (Fedora/RHEL)
```bash
sudo dnf install java-17-openjdk-devel
```

#### macOS (using Homebrew)
```bash
brew install openjdk@17
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

#### Windows (using Chocolatey)
```powershell
choco install openjdk17
```

#### Windows (Manual)
1. Download from: https://adoptium.net/
2. Extract to `C:\Program Files\Java\jdk-17`
3. Add to PATH: `C:\Program Files\Java\jdk-17\bin`

**Verify installation:**
```bash
java -version
javac -version
```

### Step 2: Set JAVA_HOME Environment Variable

#### Linux/macOS (add to ~/.bashrc or ~/.zshrc)
```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64  # Linux
# or for macOS:
export JAVA_HOME=/opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
```

#### Windows (PowerShell, run as Administrator)
```powershell
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Java\jdk-17", "User")
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";%JAVA_HOME%\bin", "User")
```

### Step 3: Download Android SDK Command-line Tools

**IMPORTANT: Use China Mirror for faster downloads in China**

#### China Mirror Sources (Recommended for users in China)

Use the following mirror URLs instead of Google's official URLs:

| Resource | Mirror URL |
|----------|------------|
| Android SDK | https://mirrors.tuna.tsinghua.edu.cn/android/repository/ |
| Android SDK (Tsinghua) | https://mirrors.tuna.tsinghua.edu.cn/android/repository/ |
| Android SDK (USTC) | https://mirrors.ustc.edu.cn/android/repository/ |

#### Linux
```bash
mkdir -p ~/Android/Sdk/cmdline-tools
cd ~/Android/Sdk/cmdline-tools

# Use China mirror (faster in China)
wget https://mirrors.tuna.tsinghua.edu.cn/android/repository/commandlinetools-linux-11076708_latest.zip

# Or use Google official (slower in China)
# wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip

unzip commandlinetools-linux-*.zip
mv cmdline-tools latest
rm commandlinetools-linux-*.zip
```

#### macOS
```bash
mkdir -p ~/Library/Android/sdk/cmdline-tools
cd ~/Library/Android/sdk/cmdline-tools

# Use China mirror (faster in China)
curl -O https://mirrors.tuna.tsinghua.edu.cn/android/repository/commandlinetools-mac-11076708_latest.zip

# Or use Google official (slower in China)
# curl -O https://dl.google.com/android/repository/commandlinetools-mac-11076708_latest.zip

unzip commandlinetools-mac-*.zip
mv cmdline-tools latest
rm commandlinetools-mac-*.zip
```

#### Windows (PowerShell)
```powershell
mkdir -p $env:LOCALAPPDATA\Android\Sdk\cmdline-tools
cd $env:LOCALAPPDATA\Android\Sdk\cmdline-tools

# Use China mirror (faster in China)
Invoke-WebRequest -Uri "https://mirrors.tuna.tsinghua.edu.cn/android/repository/commandlinetools-win-11076708_latest.zip" -OutFile "cmdline-tools.zip"

# Or use Google official (slower in China)
# Invoke-WebRequest -Uri "https://dl.google.com/android/repository/commandlinetools-win-11076708_latest.zip" -OutFile "cmdline-tools.zip"

Expand-Archive cmdline-tools.zip -DestinationPath .
Rename-Item cmdline-tools latest
Remove-Item cmdline-tools.zip
```

### Step 4: Set ANDROID_HOME Environment Variable

#### Linux/macOS (add to ~/.bashrc or ~/.zshrc)
```bash
# Linux
export ANDROID_HOME=~/Android/Sdk
# macOS
export ANDROID_HOME=~/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/emulator
```

#### Windows (PowerShell, run as Administrator)
```powershell
[System.Environment]::SetEnvironmentVariable("ANDROID_HOME", "$env:LOCALAPPDATA\Android\Sdk", "User")
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";%ANDROID_HOME%\cmdline-tools\latest\bin", "User")
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";%ANDROID_HOME%\platform-tools", "User")
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";%ANDROID_HOME%\emulator", "User")
```

**Important:** Restart your terminal or run `source ~/.bashrc` (or `source ~/.zshrc`) after setting environment variables.

### Step 5: Install Required SDK Components

Accept licenses first:
```bash
sdkmanager --licenses
```

Install essential components:
```bash
# Install build tools
sdkmanager "build-tools;34.0.0"

# Install platform tools (includes adb)
sdkmanager "platform-tools"

# Install Android platform (choose target version)
sdkmanager "platforms;android-34"

# Optional: Install emulator
sdkmanager "emulator"
sdkmanager "system-images;android-34;google_apis;x86_64"
```

### Step 6: Verify Installation

```bash
# Check SDK manager
sdkmanager --list

# Check ADB
adb version

# Check build tools
ls $ANDROID_HOME/build-tools/
```

### Step 7: Configure Gradle Mirror (REQUIRED for China)

**CRITICAL: You MUST configure Gradle to use China mirror before running any Gradle commands. Gradle downloads are extremely slow without mirror configuration.**

When you first run `./gradlew`, it will download the Gradle distribution. This download is very slow in China. You MUST modify the download URL to use a mirror.

#### Method 1: Modify gradle-wrapper.properties (RECOMMENDED)

Edit the `gradle/wrapper/gradle-wrapper.properties` file in your project:

**Original content:**
```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-8.4-bin.zip
```

**Change to (choose one mirror):**

```properties
# Tencent Cloud Mirror (Recommended)
distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-8.4-bin.zip

# Aliyun Mirror
distributionUrl=https\://mirrors.aliyun.com/gradle/gradle-8.4-bin.zip

# Huawei Cloud Mirror
distributionUrl=https\://repo.huaweicloud.com/gradle/gradle-8.4-bin.zip
```

#### Method 2: Use Environment Variable

Set environment variable before running Gradle:

**Linux/macOS:**
```bash
export GRADLE_USER_HOME=~/.gradle
mkdir -p $GRADLE_USER_HOME
```

**Windows (PowerShell):**
```powershell
$env:GRADLE_USER_HOME = "$env:USERPROFILE\.gradle"
mkdir -p $env:GRADLE_USER_HOME
```

#### Method 3: Configure Gradle Repository Mirror (for dependencies)

Create or edit `~/.gradle/init.gradle` (Linux/macOS) or `%USERPROFILE%\.gradle\init.gradle` (Windows):

```gradle
allprojects {
    repositories {
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.contains('google.com') || url.contains('maven.google.com')) {
                    project.logger.lifecycle "Repository ${repo.name} URL: ${url}"
                    remove repo
                }
                if (url.contains('jcenter.bintray.com') || url.contains('jcenter')) {
                    project.logger.lifecycle "Repository ${repo.name} URL: ${url}"
                    remove repo
                }
            }
        }
        
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/public' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
        google()
        mavenCentral()
    }
    
    buildscript {
        repositories {
            maven { url 'https://maven.aliyun.com/repository/google' }
            maven { url 'https://maven.aliyun.com/repository/public' }
            maven { url 'https://maven.aliyun.com/repository/jcenter' }
            maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
            google()
            mavenCentral()
        }
    }
}
```

**Gradle Mirror URLs:**
| Mirror | URL |
|--------|-----|
| Tencent Cloud | https://mirrors.cloud.tencent.com/gradle/ |
| Aliyun | https://mirrors.aliyun.com/gradle/ |
| Huawei Cloud | https://repo.huaweicloud.com/gradle/ |

**Maven Repository Mirrors:**
| Mirror | URL |
|--------|-----|
| Aliyun Google | https://maven.aliyun.com/repository/google |
| Aliyun Public | https://maven.aliyun.com/repository/public |
| Aliyun JCenter | https://maven.aliyun.com/repository/jcenter |
| Aliyun Gradle Plugin | https://maven.aliyun.com/repository/gradle-plugin |
| Huawei Maven | https://repo.huaweicloud.com/repository/maven/ |

### Step 8: Build an Android Project

Navigate to your Android project directory and use Gradle Wrapper:

```bash
# Clean build
./gradlew clean

# Build debug APK
./gradlew assembleDebug

# Build release APK (requires signing configuration)
./gradlew assembleRelease

# Install on connected device
./gradlew installDebug
```

**For Windows:**
```cmd
gradlew.bat clean
gradlew.bat assembleDebug
```

## Common Issues and Solutions

### Issue 1: "sdkmanager: command not found"
- Ensure `ANDROID_HOME/cmdline-tools/latest/bin` is in PATH
- Restart terminal after setting environment variables

### Issue 2: "Java version mismatch"
- Android Gradle Plugin 8.x requires JDK 17
- Check `java -version` and update JAVA_HOME accordingly

### Issue 3: "SDK location not found"
- Create or update `local.properties` in project root:
  ```
  sdk.dir=/path/to/Android/Sdk
  ```

### Issue 4: License not accepted
- Run `sdkmanager --licenses` and accept all licenses

### Issue 5: Gradle permission denied (Linux/macOS)
```bash
chmod +x gradlew
```

## Platform-Specific Notes

### Linux
- May need 32-bit libraries: `sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386`
- For emulator: `sudo apt-get install libvulkan1`

### macOS
- On Apple Silicon (M1/M2), use ARM64 versions when available
- May need to allow SDK tools in Security settings

### Windows
- Run PowerShell as Administrator for environment variable changes
- Some antivirus software may block SDK downloads

## Quick Reference Commands

| Task | Command |
|------|---------|
| List installed packages | `sdkmanager --list_installed` |
| Update all packages | `sdkmanager --update` |
| Check connected devices | `adb devices` |
| View Gradle tasks | `./gradlew tasks` |
| Build and install | `./gradlew installDebug` |

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

For automated builds, use this minimal setup:

```bash
# Set environment variables
export ANDROID_HOME=/opt/android-sdk
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools

# Download and setup SDK (using China mirror)
mkdir -p $ANDROID_HOME/cmdline-tools
cd $ANDROID_HOME/cmdline-tools
curl -O https://mirrors.tuna.tsinghua.edu.cn/android/repository/commandlinetools-linux-11076708_latest.zip
unzip commandlinetools-linux-*.zip
mv cmdline-tools latest

# Accept licenses and install components
yes | sdkmanager --licenses
sdkmanager "build-tools;34.0.0" "platform-tools" "platforms;android-34"

# Configure Gradle mirror (CRITICAL for China)
mkdir -p ~/.gradle
cat > ~/.gradle/init.gradle << 'EOF'
allprojects {
    repositories {
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/public' }
        google()
        mavenCentral()
    }
    buildscript {
        repositories {
            maven { url 'https://maven.aliyun.com/repository/google' }
            maven { url 'https://maven.aliyun.com/repository/public' }
            google()
            mavenCentral()
        }
    }
}
EOF
```

## Additional Resources

- Android SDK Command-line Tools: https://developer.android.com/studio#command-tools
- Gradle Plugin Release Notes: https://developer.android.com/build/releases/gradle-plugin
- Android Build Overview: https://developer.android.com/build

---

**Note:** This setup provides all necessary tools for building Android applications from the command line. For debugging and advanced features, you may still want Android Studio, but it is not required for building APKs.
