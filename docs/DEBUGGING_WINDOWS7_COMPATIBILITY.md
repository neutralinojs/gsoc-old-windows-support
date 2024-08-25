# Debugging Windows 7 Compatibility for Neutralinojs

## Introduction

This document outlines the steps, findings, and proposed solutions related to adding Windows 7 support to Neutralinojs.

## Initial Setup and Issues

### Installation and Project Creation

1. Successfully installed `@neutralinojs/neu` globally.
2. Created a new Neutralinojs project using:
   ```powershell
   powershell -ExecutionPolicy Bypass -Command "neu create my-project"
   ```
   Note: The `-ExecutionPolicy Bypass` flag was necessary to bypass script execution restrictions on Windows 7.

### Running the Project

Encountered an error when attempting to run the project:

```powershell
powershell -ExecutionPolicy Bypass -Command "neu run"
```

**Error Details:**
- Error Code: 3
- Description: Neutralinojs executable (`neutralino-win_x64.exe`) stops with error code 3.

## Specific Issue: Missing `CoIncrementMTAUsage` Function

### Error Message

```plaintext
The procedure entry point CoIncrementMTAUsage could not be located in the dynamic link library ole32.dll.
```

### Analysis

1. **Function Availability:**
   - `CoIncrementMTAUsage` is only available in Windows 8.0, 8.1, and 10.
   - The `winrt::Windows::` namespace is functional only on Windows 10.

2. **Alternative Libraries:**
   - Qt does not call `CoIncrementMTAUsage`.
   - Windows Runtime Library (WRL) is not compatible with Windows 7.

3. **Compilation Considerations:**
   - Including `winrt::Windows::UI::ViewManagement` is not viable for Windows 7.
   - Using `#ifdef` to compile separate versions for Windows 7 and 10 is possible but not preferred due to maintenance overhead.

## Proposed Solution: Dynamic Linking Approach

To maintain a single codebase while ensuring Windows 7 compatibility, we propose a dynamic loading approach:

1. **Code Separation:**
   - Identify and isolate code sections using `CoIncrementMTAUsage`.
   - Move this code into a separate DLL.

2. **Dynamic Loading Implementation:**
   - Use `QLibrary` or `LoadLibrary` to load the DLL at runtime.
   - Implement version checking to load the DLL only on supported Windows versions.


## Conclusion

This approach allows for maintaining a single codebase while ensuring compatibility with both Windows 7 and later versions. It requires careful management of dynamically loaded functions but provides a flexible solution for cross-version support.