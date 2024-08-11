
BOAZ 
Here to see if he stole my methods.

AV evasion methods in modern OS's is the paper he has. 
He has evasion frameworks and techniques. Gave categorization of evasion techniques. 
He wanted to bring AV testing up to date, fingerprint AV while treating like a black box.
Dev evasion categorisation etc.

Some inner workings, Filesystem write. Suspicious function, network connections, suspiciouss deletions. 
Check paper for fig 1-3

Line between AV and edr is blurred. 

Most effective attack post is in a user process, as author of malware we have control here. 
Inner working for AV, it is categorized into unpackers, scanners, sensors, and emulators.  EDR uses sensors for etw, api hooking etc,  ^335b12

Evasion categorization
- bypass passive scan upon drop to disk
- bypass active scan by installed sec products and achieve objective
- no conspicuous artifacts
- Bypassing detection does not guarantee payload persistence. So i other stuff can trip us up. And the payload can be deleted by cloud folks. 
Evasion categorization
- proposed in the research,  tamper dimension, pre/post/execution phases.
- signature evasion
- behavioral evasion
- heuristic evasion etc.

BOAZ is the tool.
Has a new encoder part, obfuscater part, new patching, new anti-emulation and hooking.  In its core are loaders for the code executoin or where to put the shell code. 
By layers for BOAZ, the pieces we use to construct the bin.  The core is the shellcode, or PE we want executed. we convert this shellcode using a converter like donut or other tools. we put the assembly or encoded stuff in to encryption schemes.  From here we put this into our code and apply anti-emulation/hooking and other code-base techniques.  We have DLL Loaders, other system calls we can use for VM or obfuscation.  And we can use different compilers which impacts detection rate (use a weird one)
After scraping the symbols, you can put things in new sections to get a cert for binary, copy metadata from legitimate binary. 

The center of the tool is the code execution loader.
- To execute shellcode you have to get a handle, ensure perms, allocate memory, write memory, and execute.

Syscalls, have been recognized as an effective method of code injection/execution. It gives a channel to talk to ring 0 through ntdll or other dlls. 
Using hookbastard? Is that what it's called checking hooked processes by other things. And own dll injection procs being hooked so we can view args passed to functions. 

AV hooking different functions, using fsecure you can see all of the hooks. commodore, etc. 
- the point is, almost all things hook ntdll.
Syscalls, sample triggers signature scans can only do when sensitive execution related api calls are actively performed such as createthread/createremotethread and createprocess. AVs conduct memory scans for allocations such as mem_private and another write one. 

To execute the shellcode, we need 4 lines of assembly
mov r10,rcx
mov eax, ssn
syscall
return

^ Direct syscall^
but has problems becausee callstack doesn't originate from legit place. 
Syscall projects to use hooking or diff syscalls

Dumpert
syscall tables to arch
syswhispers 1- 3
freshycalls
hells gate tartarus gate halos gate
youmaypasser
shellwasp
ntdll unhooking. 

Halo's gate exploits the architecture and syscall numbers being incremental, so if one function is being hooked, we can search until we find a not hooked function and incrememnt until we get to the correct place. 

NTDLL Loading. In order to get NTDLL loaded, we can get the process environmental block positionsed form the gx registers and search for 64kb stabs until we have a valid NTDL and DOS Header. 

Another method is via module leased. 
Custom call stacks
- Direct syscalls suffer from and originate from our own bad portable executable memory.  These jump back to ntdll to pass shellcode.  But can go to sus memory regions. 
- We originate from the RTRU. 
	- kernel 32. 

Loader 1
- Custom call stack loader is here
- custom stack for nt api calls, stack traces, and return addresses within ntdll
- Technique proposed by paranoid injure. 
- Have an assembly statement, put all the args into first 4 registers, rest on the stack. 
- process memory
- just jmp rax to jump to 
uses worker calls to call the function size

Loader 40
- for local process injection we can call nt createthread, but calling this nt function, we can further implement new ways of remote process injection via dll notification callback. this is then threadless.
- We use the this for every function 
- Caro Kann technique by securethishit
	- Pic converter converts things to shellcode, and doing this creates shellcode that first executes trumping shellcode with a sleep. 
	- Executing work insie to execute real shellcode. applying the evasion to mimikatz, 
	- the metasploit reverse tcp shell can be obfuscated using this technique and using BOAZ.

Loader 47
- Can find a dotnet dll to transact it, or create another file and map to the target process in memory using either ldr or dll nt create detection and nt map view of section
- doing so we already have rx perms and memories of image, and don't need to allocate memory as we already have a place to inject
- we can se ntapc ex2 to hook things successfully, and use the functions to queue things and move the memory appropriately, and write our shell into target mem process.
- catch is if we don't use rdrldll we don't have our module in the peb, which would read an FP in the module scanner, which wil get mad at "missing peb"
- can use the code to just add our dll info into the module leased. 
- we also need to change a flag so it's not executed when doing the function to add the module list. 
- execution is depent on loading our dll entry point, and doing other things like creating a jump to the dll entry point.  We can also use a function which is similar to the apc scribe, which uses askapc to execute the shellcode, and disrupt the usual api succession sequence.  Doing this we replace all of the primitives inside the program, and our conventional module overload or virtual alloc is possible

Custom DLL loader 

And a new memory guard, because memory scanners and monitors willl check scanned memory pages for working sets to see if there's chane, the orking set stores information about shared pages and is actually checked if the pages and if it's null accessible, if it's null accessible then it's a legit windows flag to use. 
So we'll store things for ourselves. 
In this specific new memory guard, we'll use debuggers dr0-3, debug register to location we want. Deeper into thread execution. 

Can extend all of this to more detection points, and change our informaiton back, and rdx registers to the real start address and real execution with this new memory guard.  We can change values to do these things. 

Using mimikatz as our example shellcode, you can input this into the loader tool and it will be undetected by bitdefender. 

So we use dll loader, wrap shellcode, and execute things. Gives us mimikatz, and we can execute commands like coffee. original gets deleted, but not the wrapped payload. 

First he used a classic process injection wrapped, and found different things by moneta, private memory stuff not finding the same detection? IDk this guy's hard to understand to any degree.

if you enable all handles, and use moneta sacnner, moneta doesn't detect??? I dunno again, hard to understand. 

Mimikatz in shellcode. worked?

Blackbox testing then works on these antiviruses, he can show this in the report. 
He stacked the evasion frameworks for testing.  The famous ones, going to have to just read dude's paper. 

AV. over-reliance on signatures give high FP rates,  a balance between FP and TP rates causes high CPU overhead, 

So within his experiments, using a loader that does nothing, they were able to get calc to to pop things as malicious on some AVs because it did certain evasion things. 
So high FP and TP, has a malicious payload w/o too much collateral damage. 
malware can blend in by using these bad patterns. 

AVs don't agree on labeling for detection rates. 
AVs are susceptible to byte injections, it suspends and deletes CMD and powershell that ran samples, but left the sample so it can delete good programs if you inject them with sus bytes the correct way (LOL)
Oh *stuff*, this is how most of my stuff works.

All AVs can detect UPX on signature. 
Only one AV quarantined sample based on a behavioral detection during execution
AVs lack iaccurate/informative labeling
8 AVs have sandboxing/emulation to detect bad shtuff

Only 3 AVs can decode XOR encoding, only 2 AVs can detect 3 detection mechanisms in 
sequence. 


Userland hooks are vulnerable
position independent code is crucial
encoding and obsucation are a beginning
loader plays vital role in evading behavioral detections

He didn't give his tool >:(