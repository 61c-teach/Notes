 To enable the RISC-V compiler and emulator, run

 `~ $ source ~cs61c/riscv-activate`

on a hive machine. This will give you access to the commands

```console
riscv-gcc
riscv-emu
```

which compile C to RISC-V and execute compiled RISC-V code, respectively. For example,
```c
~ $ source ~cs61c/riscv-activate
~ $ cat hello.c
#include <stdio.h>

int main(void) {
    printf("Hello world!\n");
    return 0;
}
```

```console
~ $ riscv-gcc -S -o hello.s hello.c # hello.s contains RISC-V assembly
~ $ riscv-gcc -o hello hello.c      # hello contains a RISC-V executable
~ $ riscv-emu hello
Hello world!
```

There are a few minor differences between "real-world" RISC-V and what we teach in 61C:

    `ret == jalr x0, ra, 0`
    `call label == jal ra, label`
    The stack is aligned to 16 bytes, rather than 4. This means that the prologue/epilogue of functions, if applicable, will have `addi sp, sp, -16` (or -32, -48, etc.) instead of the 4 byte offsets that are shown in class.

Happy compiling!
