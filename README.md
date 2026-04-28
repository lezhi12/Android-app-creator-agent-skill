# Android Build Environment Setup Skill

A Trae IDE skill for setting up Android app build environment without installing Android Studio. Supports Linux, Windows, and macOS platforms.

## Features

- ✅ **Cross-platform support** - Linux, Windows, macOS
- ✅ **No Android Studio required** - Pure command-line setup
- ✅ **Smart environment check** - Skips already installed components
- ✅ **China mirror support** - Faster downloads for users in China
- ✅ **Gradle optimization** - Mirror configuration for faster builds
- ✅ **CI/CD ready** - Minimal setup scripts for automation

## Quick Start

### Using in Trae IDE

This skill is automatically invoked when you ask Trae to:
- Set up Android build environment
- Build Android apps without Android Studio
- Configure Android SDK command-line tools

### Manual Setup

1. **Check existing environment** (Step 0)
   ```bash
   java -version
   echo $ANDROID_HOME
   gradle -v
   ```

2. **Install JDK** (if not installed)
   ```bash
   # Linux
   sudo apt install openjdk-17-jdk
   
   # macOS
   brew install openjdk@17
   
   # Windows
   choco install openjdk17
   ```

3. **Download Android SDK** (using China mirror for faster downloads)
   ```bash
   # Linux/macOS
   wget https://mirrors.tuna.tsinghua.edu.cn/android/repository/commandlinetools-linux-11076708_latest.zip
   ```

4. **Configure Gradle mirror** (REQUIRED for China)
   ```properties
   # Edit gradle-wrapper.properties
   distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-8.4-bin.zip
   ```

5. **Build your project**
   ```bash
   ./gradlew assembleDebug
   ```

## Key Highlights

### 🔍 Environment Check First

The skill automatically checks for existing tools before installing:
- JDK and JAVA_HOME
- Android SDK and components
- Gradle installation

### 🚀 China Mirror Support

All downloads use China mirrors by default:
- **Android SDK**: Tsinghua, USTC mirrors
- **Gradle**: Tencent Cloud, Aliyun, Huawei Cloud
- **Maven**: Aliyun repository mirrors

### ⚠️ Global Gradle Warning

The skill warns about global Gradle conflicts and recommends using Gradle Wrapper instead.

## Components Installed

| Component | Purpose |
|-----------|---------|
| JDK 11/17 | Java development kit |
| Android SDK Command-line Tools | Core SDK tools |
| Android SDK Build Tools | Compiling and packaging |
| Android SDK Platform Tools | ADB and other tools |
| Android Platform | Target SDK version |

## Common Use Cases

- **Local development** - Build Android apps without Android Studio
- **CI/CD pipelines** - Automated build environments
- **Lightweight setups** - Minimal disk usage
- **Server builds** - Headless build servers

## Documentation

See [SKILL.md](./SKILL.md) for complete documentation including:
- Step-by-step setup instructions
- Platform-specific notes
- Troubleshooting guide
- CI/CD configuration examples

## Requirements

- Operating System: Linux, Windows, or macOS
- Internet connection (or use offline mirrors)
- Basic command-line knowledge

## License

MIT License

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## Author

Created for Trae IDE to simplify Android development environment setup.
