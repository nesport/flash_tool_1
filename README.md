# QDL Wrapper - Qualcomm EDL Mode Flashing Tool

A comprehensive .NET wrapper for `qdl.exe` that provides both CLI and GUI interfaces for flashing Qualcomm devices in Emergency Download (EDL) mode.

## ⚠️ Important Warnings

**READ THIS BEFORE USING:**

- **Device Damage Risk**: Flashing firmware can permanently damage your device if done incorrectly. Use at your own risk.
- **Legal Use Only**: This tool is intended for legitimate purposes only (device recovery, development, etc.). Use only signed programmers for production devices.
- **No Security Bypass**: This tool does NOT help bypass:
  - Device signature verification
  - FRP (Factory Reset Protection) locks
  - Bootloader locks
  - OEM security features
- **Signed Programmers Required**: Production devices require properly signed programmer files. Unsigned or incorrectly signed programmers will fail to load.
- **User Responsibility**: You accept all responsibility for the consequences of using this tool.

## Features

### CLI Application
- ✅ Simple command-line interface: `QdlWrapper.exe <prog.mbn> <rawprogram.xml> [patch.xml ...]`
- ✅ Automatic `qdl.exe` detection with clear error messages
- ✅ Proper argument escaping (supports paths with spaces)
- ✅ Real-time stdout/stderr capture and display
- ✅ Progress tracking with visual progress bar
- ✅ Comprehensive logging with timestamps
- ✅ Returns `qdl.exe` exit code
- ✅ Ctrl+C support for aborting operations
- ✅ Clear error messages for common issues

### GUI Application (WPF)
- ✅ Modern, user-friendly interface
- ✅ Drag & drop support for files
- ✅ File picker dialogs for programmer, rawprogram, and patch files
- ✅ Real-time output console with auto-scroll
- ✅ Visual progress bar with percentage
- ✅ Start/Abort/Open Logs buttons
- ✅ Settings dialog for configuration
- ✅ Configuration persistence (saved to AppData)
- ✅ Support for multiple patch files

### Core Features
- ✅ Async/await process management
- ✅ Real-time output parsing for state and progress
- ✅ Detailed logging with timestamps
- ✅ Comprehensive error handling
- ✅ Unit and integration tests

## Requirements

- **Operating System**: Windows 10/11 (x64)
- **.NET Runtime**: .NET 7.0 or later
- **QDL Tool**: `qdl.exe` must be present (see Installation section)
- **USB Driver**: WinUSB driver for EDL device (install via Zadig)
- **Device**: Qualcomm device in EDL mode (port 9008)

## Installation

### 1. Install .NET 7 Runtime

Download and install the .NET 7 Runtime from:
- https://dotnet.microsoft.com/download/dotnet/7.0

Choose the **Desktop Runtime** for GUI, or **Runtime** for CLI only.

### 2. Build QDL Wrapper from Source

```bash
# Navigate to the project directory
cd D:\Dev\Flash_tool

# Restore dependencies
dotnet restore

# Build all projects
dotnet build --configuration Release

# Binaries will be in:
# - CLI: src/QdlWrapper.Console/bin/Release/net7.0/
# - GUI: src/QdlWrapper.Gui/bin/Release/net7.0-windows/
```

**For standalone executables** (includes .NET runtime, no installation needed):
```bash
# CLI (self-contained)
dotnet publish src/QdlWrapper.Console/QdlWrapper.Console.csproj -c Release -r win-x64 --self-contained -p:PublishSingleFile=true

# GUI (self-contained)
dotnet publish src/QdlWrapper.Gui/QdlWrapper.Gui.csproj -c Release -r win-x64 --self-contained -p:PublishSingleFile=true
```

### 3. Obtain qdl.exe

You need the `qdl.exe` tool. Place it in the same directory as `QdlWrapper.exe`.

**Where to get qdl.exe:**
- Build from source: https://github.com/linux-msm/qdl
- Or search for pre-built binaries (ensure from trusted sources)

**Important**: Verify the integrity of `qdl.exe` before use.

### 4. Install WinUSB Driver (Zadig)

For Windows to communicate with EDL devices, you need to install the WinUSB driver:

1. Download **Zadig** from: https://zadig.akeo.ie/
2. Boot your device into EDL mode (device should show as port 9008 or "QDLoader 9008")
3. Run Zadig as Administrator
4. In Zadig:
   - Go to **Options** → **List All Devices**
   - Select your device (usually shows as "QHSUSB__BULK" or "QDLoader 9008")
   - Select **WinUSB** as the target driver
   - Click **Replace Driver** or **Install Driver**
5. Wait for installation to complete

**Troubleshooting**: If device doesn't appear in Zadig, try different USB ports, cables, or check Device Manager.

## Usage

### CLI Usage

```bash
QdlWrapper.exe <prog.mbn> <rawprogram.xml> [patch.xml ...]
```

**Arguments:**
- `prog.mbn` - Programmer/loader file (required)
- `rawprogram.xml` - Raw program XML file (required)
- `patch.xml` - Patch XML file(s) (optional, can specify multiple)

**Examples:**

```bash
# Basic usage with programmer and rawprogram
QdlWrapper.exe prog_firehose.mbn rawprogram0.xml

# With patch files
QdlWrapper.exe prog_firehose.mbn rawprogram0.xml patch0.xml patch1.xml

# With paths containing spaces
QdlWrapper.exe "C:\Firmware\prog firehose.mbn" "C:\Firmware\rawprogram 0.xml"
```

**Features:**
- Real-time progress bar in console
- Color-coded status messages
- Automatic log file creation in `logs/` directory
- Ctrl+C to abort operation
- Exit code matches `qdl.exe` exit code

### GUI Usage

1. Run `QdlWrapper.Gui.exe`
2. Select files:
   - **Programmer File (.mbn)**: Click "Browse..." or drag & drop
   - **Rawprogram File (.xml)**: Click "Browse..." or drag & drop
   - **Patch Files (.xml)** (optional): Click "Add..." or drag & drop
3. Click **Start** to begin flashing
4. Monitor progress in the output window
5. Use **Abort** to stop operation if needed
6. Click **Open Logs** to view detailed logs
7. Click **Settings** to configure QDL path and logging options

**Settings:**
- QDL Executable Path (default: `qdl.exe` in app directory)
- Log Directory (default: `logs/`)
- Enable/Disable Logging
- Device Timeout (default: 60 seconds)

**Configuration is automatically saved** to:
```
%LOCALAPPDATA%\QdlWrapper\config.json
```

## Logs

All operations are logged with detailed timestamps to help troubleshoot issues.

**Log Location:**
- Default: `logs/` directory in the application folder
- Log filename format: `qdl_YYYYMMDD_HHMMSS.log`

**Log Contents:**
- Timestamps for each event (millisecond precision)
- Full stdout/stderr from `qdl.exe`
- State changes and progress updates
- Errors and warnings

**Opening Logs:**
- CLI: Check the log file path printed at startup
- GUI: Click the **Open Logs** button

## Testing

### Running Unit Tests

```bash
# Run all tests
dotnet test

# Run with verbose output
dotnet test --verbosity normal

# Run specific test project
dotnet test tests/QdlWrapper.Tests/QdlWrapper.Tests.csproj
```

### Test Coverage

The project includes comprehensive tests:

- **QdlOutputParserTests**: Tests for parsing QDL output and extracting progress
- **QdlLoggerTests**: Tests for logging functionality with timestamps
- **ConfigurationManagerTests**: Tests for configuration save/load
- **QdlProcessIntegrationTests**: Integration tests for process management

### Manual Testing on Real Device

**Prerequisites:**
1. Qualcomm device in EDL mode (port 9008)
2. WinUSB driver installed via Zadig
3. Valid firmware files (signed programmer, rawprogram, patches)

**Test Procedure:**
1. Connect device in EDL mode
2. Verify device appears in Device Manager as "QDLoader 9008" (or similar)
3. Run QdlWrapper (CLI or GUI) with valid files
4. Observe:
   - Device detection message
   - Progress updates (0-100%)
   - Real-time output
   - Completion status
5. Check log file for detailed information
6. Verify device functionality after flashing

**Common Test Scenarios:**
- ✅ Device detection in EDL mode
- ✅ Progress bar updates correctly
- ✅ Log file created with timestamps
- ✅ Abort operation works (Ctrl+C or GUI button)
- ✅ Error handling (missing files, device disconnection)
- ✅ Successful completion
- ✅ Exit code matches expected value

## Common Issues and FAQ

See [FAQ.md](docs/FAQ.md) for detailed troubleshooting information.

**Quick Troubleshooting:**

| Issue | Solution |
|-------|----------|
| "qdl.exe not found" | Place `qdl.exe` in the same directory as QdlWrapper.exe |
| "Device not found" | Ensure device is in EDL mode and WinUSB driver is installed |
| "Access denied" | Run as Administrator or check USB permissions |
| "Timeout waiting for device" | Check USB cable, try different port, reinstall driver |
| Operation fails immediately | Verify programmer file is signed and compatible |

## Project Structure

```
QdlWrapper/
├── src/
│   ├── QdlWrapper.Core/          # Core library (shared logic)
│   │   ├── Models/                # Data models
│   │   └── Services/              # Core services
│   ├── QdlWrapper.Console/        # CLI application
│   └── QdlWrapper.Gui/            # WPF GUI application
├── tests/
│   └── QdlWrapper.Tests/          # Unit and integration tests
├── docs/
│   ├── FAQ.md                     # Frequently asked questions
│   └── TESTING.md                 # Testing instructions
├── templates/
│   └── config.json                # Example configuration
├── QdlWrapper.sln                 # Solution file
├── README.md                      # This file
└── LICENSE                        # License information
```

## Development

### Building

```bash
# Restore dependencies
dotnet restore

# Build Debug
dotnet build

# Build Release
dotnet build --configuration Release

# Publish CLI (self-contained)
dotnet publish src/QdlWrapper.Console/QdlWrapper.Console.csproj -c Release -r win-x64 --self-contained

# Publish GUI (self-contained)
dotnet publish src/QdlWrapper.Gui/QdlWrapper.Gui.csproj -c Release -r win-x64 --self-contained
```

### Code Style

- Use async/await for I/O operations
- Comprehensive XML documentation comments
- Follow C# naming conventions
- Include error handling with meaningful messages
- Log important events and errors

### Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Write tests for new functionality
4. Ensure all tests pass
5. Submit a pull request

## Security Considerations

- **No Secrets**: Never commit secrets, keys, or sensitive data
- **Input Validation**: All file paths and arguments are validated
- **Process Isolation**: QDL process runs with limited privileges
- **Log Sanitization**: Logs do not contain sensitive information
- **No Security Bypass**: Tool does not include any security bypass functionality

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- **qdl.exe**: Developed by linux-msm project (https://github.com/linux-msm/qdl)
- **Zadig**: Universal USB driver installer (https://zadig.akeo.ie/)

## Disclaimer

This software is provided "as is", without warranty of any kind. The authors are not responsible for any damage caused by the use of this software. Use at your own risk.

**This tool is for legitimate purposes only.** Do not use it to bypass security measures, violate terms of service, or engage in any illegal activities.

## Support

For issues, questions, or contributions:
- Check the [FAQ.md](docs/FAQ.md) for common problems
- Review [TESTING.md](docs/TESTING.md) for troubleshooting
- Check existing issues in the project documentation

## Changelog

### Version 1.0.0 (Initial Release)
- ✅ CLI application with progress tracking
- ✅ WPF GUI application
- ✅ Real-time output capture and parsing
- ✅ Comprehensive logging with timestamps
- ✅ Unit and integration tests
- ✅ Full documentation

---

**Remember**: Always use signed programmers, verify firmware files, and understand the risks before flashing devices.


