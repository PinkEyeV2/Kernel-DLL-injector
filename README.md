PS: Readme Is already included in folder 

Kernel Mode to User Mode DLL Injection (Windows x64 1909)
Inject a DLL into a target process from a kernel driver.

How It Works
The injection process is divided into several stages:

Attach to Target Process Address Space

Attach the current kernel thread to the virtual address space of the target process using KeStackAttachProcess.

Locate LoadLibraryW

Get the target process's PEB via PsGetProcessPeb.

Iterate over all loaded modules to find kernel32.dll.

Parse kernel32.dll's PE headers to locate the address of LoadLibraryW.

Allocate Memory in Target Process

Allocate memory for APC callback arguments (RW) using ZwAllocateVirtualMemory.

Allocate memory for the APC callback itself (RWX) using ZwAllocateVirtualMemory.

Detach from Target Process

Detach from the target process address space with KeUnstackDetachProcess.

Find Target Process Threads

Use ZwQuerySystemInformation(SystemProcessInformation) to enumerate processes.

Locate the target process by PID and retrieve its thread IDs.

Inject and Queue APC

Prepare an APC using KeInitializeApc.

Insert the APC into a target thread’s queue with KeInsertQueueApc.

The APC executes the callback, which invokes LoadLibraryW to load the DLL.

Note:
The entire injection process runs in the kernel driver.
The driver only needs two things: the target process PID and the DLL path.

Limitations
x64 Binaries Only:
Only 64-bit binaries are supported.

Memory Leaks:
The current version does not free memory allocated for the APC callback or its arguments.

Usage
bash
Copy
Edit
sc create DllInjector binPath= {driver_path} type= kernel
sc start DllInjector

DLLInjectorCom.exe {dll_path} {pid}

# DONE!!!
Requirements
Windows 10 x64 Version 1909

Administrator privileges

Proper driver signing / test mode enabled

