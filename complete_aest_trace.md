# Complete AEST Trace Example: Hello World with Line Feed (code source from asmtutor.com Lesson 7 - https://asmtutor.com/#lesson2

This is a complete Assembly Execution State Tracking (AEST) trace for the hello world program that uses the `sprintLF` function.

## Program Overview
- **Main program**: `helloworld-lf.asm` 
- **Functions**: `functions.asm` (slen, sprint, sprintLF, quit)
- **Purpose**: Print two messages with line feeds, then exit

## AEST Trace Table

| Line | Function | Instruction | Variables/Description | ZF | R1(eax) | R2(ebx) | R3(ecx) | R4(edx) | R5(esp) | S1 | S2 | S3 | S4 | S5 |
|------|----------|-------------|----------------------|----|---------|---------|---------|---------|---------|----|----|----|----|----|
| 17 | main | `mov eax, msg1` | Load address of "Hello, brave new world!" | N | msg1_addr | ? | ? | ? | stack_top | | | | | |
| 18 | main | `call sprintLF` | Call print with linefeed | N | msg1_addr | ? | ? | ? | stack_top-8 | ret_addr | | | | |
| 47 | sprintLF | `call sprint` | Call basic print function | N | msg1_addr | ? | ? | ? | stack_top-16 | ret_addr | ret_addr2 | | | |
| 23 | sprint | `push edx` | Save edx register | N | msg1_addr | ? | ? | ? | stack_top-24 | ret_addr | ret_addr2 | old_edx | | |
| 24 | sprint | `push ecx` | Save ecx register | N | msg1_addr | ? | ? | ? | stack_top-32 | ret_addr | ret_addr2 | old_edx | old_ecx | |
| 25 | sprint | `push ebx` | Save ebx register | N | msg1_addr | ? | ? | ? | stack_top-40 | ret_addr | ret_addr2 | old_edx | old_ecx | old_ebx |
| 26 | sprint | `push eax` | Save string address | N | msg1_addr | ? | ? | ? | stack_top-48 | ret_addr | ret_addr2 | old_edx | old_ecx | old_ebx |
| 27 | sprint | `call slen` | Call string length function | N | msg1_addr | ? | ? | ? | stack_top-56 | ret_addr | ret_addr2 | old_edx | old_ecx | old_ebx |

### Inside slen function (calculating string length):

| Line | Function | Instruction | Variables/Description | ZF | R1(eax) | R2(ebx) | R3(ecx) | R4(edx) | R5(esp) | S1-S6 (abbreviated) |
|------|----------|-------------|----------------------|----|---------|---------|---------|---------|---------|---------------------|
| 4 | slen | `push ebx` | Save ebx | N | msg1_addr | ? | ? | ? | stack_top-64 | ...old_ebx2 |
| 5 | slen | `mov ebx, eax` | ebx = start address | N | msg1_addr | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 8 | slen | `cmp byte [eax], 0` | Check if 'H' == 0 | N | msg1_addr | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 9 | slen | `jz finished` | Not zero, continue | N | msg1_addr | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 10 | slen | `inc eax` | Point to 'e' | N | msg1_addr+1 | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 11 | slen | `jmp nextchar` | Loop back | N | msg1_addr+1 | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 8 | slen | `cmp byte [eax], 0` | Check if 'e' == 0 | N | msg1_addr+1 | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| ... | ... | ... | (Continue for each character) | ... | ... | ... | ... | ... | ... | ... |
| 8 | slen | `cmp byte [eax], 0` | Check null terminator | Y | msg1_addr+24 | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 9 | slen | `jz finished` | Found null, jump | Y | msg1_addr+24 | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 14 | slen | `sub eax, ebx` | Length = 24-0 = 24 | N | 24 | msg1_addr | ? | ? | stack_top-64 | ...old_ebx2 |
| 15 | slen | `pop ebx` | Restore ebx | N | 24 | old_ebx | ? | ? | stack_top-56 | ...original_stack |
| 16 | slen | `ret` | Return to sprint | N | 24 | old_ebx | ? | ? | stack_top-48 | ...original_stack |

### Back in sprint function (setting up system call):

| Line | Function | Instruction | Variables/Description | ZF | R1(eax) | R2(ebx) | R3(ecx) | R4(edx) | S1-S5 (abbreviated) |
|------|----------|-------------|----------------------|----|---------|---------|---------|---------|---------------------|
| 29 | sprint | `mov edx, eax` | edx = string length (24) | N | 24 | old_ebx | ? | 24 | ...msg1_addr |
| 30 | sprint | `pop eax` | Restore string address | N | msg1_addr | old_ebx | ? | 24 | ...original_stack |
| 32 | sprint | `mov ecx, eax` | ecx = string address | N | msg1_addr | old_ebx | msg1_addr | 24 | ...original_stack |
| 33 | sprint | `mov ebx, 1` | ebx = stdout | N | msg1_addr | 1 | msg1_addr | 24 | ...original_stack |
| 34 | sprint | `mov eax, 4` | eax = sys_write | N | 4 | 1 | msg1_addr | 24 | ...original_stack |
| 35 | sprint | `int 80h` | System call: write | N | 4 | 1 | msg1_addr | 24 | ...original_stack |
| 37 | sprint | `pop ebx` | Restore registers | N | 4 | old_ebx | msg1_addr | 24 | ...original_stack |
| 38 | sprint | `pop ecx` | Restore registers | N | 4 | old_ebx | old_ecx | 24 | ...original_stack |
| 39 | sprint | `pop edx` | Restore registers | N | 4 | old_ebx | old_ecx | old_edx | ...original_stack |
| 40 | sprint | `ret` | Return to sprintLF | N | 4 | old_ebx | old_ecx | old_edx | ...original_stack |

### Back in sprintLF function (adding line feed):

| Line | Function | Instruction | Variables/Description | ZF | R1(eax) | R2(ebx) | R3(ecx) | R4(edx) | R5(esp) |
|------|----------|-------------|----------------------|----|---------|---------|---------|---------|---------|
| 49 | sprintLF | `push eax` | Save current eax | N | 4 | old_ebx | old_ecx | old_edx | stack_top-8 |
| 50 | sprintLF | `mov eax, 0Ah` | Load linefeed char | N | 0x0000000A | old_ebx | old_ecx | old_edx | stack_top-8 |
| 53 | sprintLF | `push eax` | Push linefeed to stack | N | 0x0000000A | old_ebx | old_ecx | old_edx | stack_top-16 |
| 57 | sprintLF | `mov eax, esp` | eax = address of linefeed | N | stack_top-16 | old_ebx | old_ecx | old_edx | stack_top-16 |
| 58 | sprintLF | `call sprint` | Print the linefeed | N | stack_top-16 | old_ebx | old_ecx | old_edx | stack_top-24 |

*[sprint function executes again, printing the linefeed character]*

| 59 | sprintLF | `pop eax` | Remove linefeed | N | 0x0000000A | old_ebx | old_ecx | old_edx | stack_top-8 |
| 60 | sprintLF | `pop eax` | Restore original eax | N | 4 | old_ebx | old_ecx | old_edx | stack_top |
| 61 | sprintLF | `ret` | Return to main | N | 4 | old_ebx | old_ecx | old_edx | stack_top+8 |

### Second message (msg2):

*[The same process repeats for msg2 - "This is how we recycle in NASM."]*

### Program exit:

| Line | Function | Instruction | Variables/Description | ZF | R1(eax) | R2(ebx) |
|------|----------|-------------|----------------------|----|---------|---------|
| 67 | quit | `mov ebx, 0` | Set exit status = 0 | N | ? | 0 |
| 68 | quit | `mov eax, 1` | Set sys_exit call | N | 1 | 0 |
| 69 | quit | `int 80h` | System call: exit | N | 1 | 0 |

## Key Learning Points

1. **Function Call Stack**: Notice how return addresses accumulate on the stack with each `call`
2. **Register Preservation**: Functions save/restore registers they modify
3. **Parameter Passing**: String address passed via `eax` register
4. **System Call Setup**: Specific registers required for `write` and `exit` system calls
5. **Stack Management**: Critical to maintain proper push/pop balance

## Notes

- **msg1_addr**: Represents the actual memory address where "Hello, brave new world!" is stored
- **stack_top**: Represents the current top of stack (ESP register)
- **old_xxx**: Represents the previous value of register xxx before function call
- **ZF**: Zero Flag - set to Y only when comparison results in zero

This trace shows the complete execution flow for printing two strings with line feeds and exiting properly.
