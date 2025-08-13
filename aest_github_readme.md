# Assembly Execution State Tracking (AEST)

## What is AEST?

Assembly Execution State Tracking (AEST) is a systematic approach to manually tracking assembly program execution. It helps learners understand exactly what happens at each instruction by maintaining a detailed state table.

## Why Use AEST?

- **Visual Learning**: See how registers, flags, and stack change step-by-step
- **Debug Understanding**: Trace program flow before running code
- **Concept Reinforcement**: Forces deep thinking about each instruction
- **Error Prevention**: Catch logic errors during the design phase

## How It Works

Create a spreadsheet/table with columns for:

| Column | Purpose | Example |
|--------|---------|---------|
| Function | Which function is executing | `main`, `strlen`, `sprint` |
| Line | Program line number | `14`, `23`, `47` |
| Instruction | The actual assembly instruction | `mov eax, msg1`, `push ebx` |
| Variables | High-level description of what's happening | `Loading string address`, `Checking character 'H'` |
| ZF | Zero Flag state (Y/N) | `N` (most of the time), `Y` (when comparison equals zero) |
| R1-R15 | Register contents | `msg_start`, `msg_start+1`, `25` (length result) |
| S1-S20 | Stack contents (LIFO order) | `old_ebx`, `old_ecx`, `return_addr` |

## Example: String Length Function

```assembly
slen:
    push    ebx              ; Save ebx
    mov     ebx, eax         ; ebx = start address
nextchar:
    cmp     byte [eax], 0    ; Check for null terminator
    jz      finished         ; Jump if found null
    inc     eax              ; Move to next character
    jmp     nextchar         ; Loop back
finished:
    sub     eax, ebx         ; Calculate length
    pop     ebx              ; Restore ebx
    ret
```

### AEST Trace:

| Line | Instruction | Variables | ZF | R1(eax) | R2(ebx) | S1 |
|------|------------|-----------|----|---------|---------|----|
| 1 | `push ebx` | Save register | N | msg_start | ? | old_ebx |
| 2 | `mov ebx, eax` | Store start address | N | msg_start | msg_start | old_ebx |
| 4 | `cmp byte [eax], 0` | Check 'H' != 0 | N | msg_start | msg_start | old_ebx |
| 5 | `jz finished` | Not zero, continue | N | msg_start | msg_start | old_ebx |
| 6 | `inc eax` | Move to next char | N | msg_start+1 | msg_start | old_ebx |
| ... | ... | ... | ... | ... | ... | ... |
| 4 | `cmp byte [eax], 0` | Found null terminator | Y | msg_start+24 | msg_start | old_ebx |
| 5 | `jz finished` | Jump to finished | Y | msg_start+24 | msg_start | old_ebx |
| 9 | `sub eax, ebx` | Calculate 24-0=24 | N | 24 | msg_start | old_ebx |

## Templates

### Basic AEST Template
- [Download Spreadsheet Template](templates/aest-basic-template.ods)
- [Download CSV Template](templates/aest-basic-template.csv)

### Advanced AEST Template  
- [Download with All Registers](templates/aest-advanced-template.ods)

## Getting Started

1. **Choose your program**: Start with simple programs (hello world, basic math)
2. **Set up your table**: Use the template or create your own
3. **Trace step by step**: Go through each instruction mentally
4. **Fill in changes**: Update registers, flags, and stack after each instruction
5. **Verify with debugger**: Use GDB to confirm your trace is correct

## Tips for Success

- **Start simple**: Begin with programs without loops or functions
- **Use symbolic addresses**: `msg_start+1` instead of actual memory addresses
- **Focus on logic**: Track what the program is conceptually doing
- **Color code**: Use different colors for different data types or functions
- **Verify with GDB**: Compare your trace with actual debugging output

## Examples

- [Hello World AEST Trace](examples/hello-world-trace.md)
- [String Length Function](examples/strlen-trace.md)
- [Array Processing](examples/array-trace.md)

## Contributing

Found this helpful? Have improvements or examples to share?

1. Fork this repository
2. Add your examples or improvements
3. Submit a pull request

## Community

- **Reddit**: Join discussions at r/asm or r/learnprogramming
- **Issues**: Report bugs or suggest features in GitHub Issues
- **Discussions**: Share your AEST traces and get feedback

## License

This educational method and templates are released under MIT License. Use freely for learning and teaching!

## About

Created by Glen as a learning tool for assembly language programming. The "Glenplate Method" emerged from the frustration of tracking complex program state during assembly learning.

---

*"Understanding assembly isn't about memorizing instructions - it's about seeing the machine think, one step at a time."*
