# xcc700: Self-hosting mini C compiler for esp32 / Xtensa

## Why look into this project?

- A compiler you can fully grasp and tweak, on a modern platform where small is still cool.
- Basic features, not too entrenched, easy to morph into your language of choice.
- Reusable ELF writer, and a basic Xtensa bytecodes emitter.
- Possibly useful for hotfixes, CI, quick test/debug turnaround on esp32.

## Sample output

    ./xcc700 xcc700.c -o xcc700.elf 

    [ xcc700 ] BUILD COMPLETED > OK
    > IN  : 700 Lines / 7977 Tokens
    > SYM : 69 Funcs / 91 Globals
    > REL : 152 Literals / 1027 Patches
    > MEM : 1041 B .rodata / 17120 B .bss
    > OUT : 27735 B .text / 33300 B ELF
    [ 40 ms ] >> 17500 Lines/sec <<

Note: that timing is from esp32-s3. Timings on Mac/POSIX will be reported 1000x slower than they are, as on esp32 ticks are millisecond, and on POSIX microsecond, but there is no adjustment here.

## Video Demo



https://github.com/user-attachments/assets/2bbd4fd5-04f7-4890-abf0-5e7454345148

.

## How to run xcc700?

Several options:

**A.** Compile with `gcc xcc700.c` and run it on your computer as a cross-compiler. It is fairly portable, tested on Mac x86_64 and arm64.

**B.** Compile for esp32 using xtensa-gcc or xcc700 from the option A (yes it can compile and cross-compile itself). Or grab the gcc-compiled version here: [xcc700.elf (16kB)](release/xcc700.elf). Run with [ESP-IDF elf_loader](https://components.espressif.com/components/espressif/elf_loader/).

**C.** If you use my [BreezyBox shell component](https://github.com/valdanylchuk/breezybox), like in [this demo project](https://github.com/valdanylchuk/breezydemo/), this will download and install it into your bin folder:

    eget valdanylchuk/xcc700

**D.** Adapt the source code and call it as a function in your firmware.

## What is included?

- C features: minimum required to write something like this compiler. While loop, if/then/else, limited support for int/char/pointers/arrays, function calls and definitions, basic arithmetic and bitwise operators.
- Single source .c file as input, single REL ELF file as output.
- The output files can be run directly by the ESP-IDF elf_loader component, which links them on load via relocation table to anything you have exposed in your firmware: newlib libc, LVGL, your custom functions, anything you like. Just declare the functions you use.

## What is missing?

- The rest of the C: for/do, include/define, long/float/double, struct/union/typedef, switch/case, array initializers, .data section, multi-line comments, too much to list.
- Many features are implemented only partially. E.g. you can have .bss globals but not global initializers; ++/-- are only supported in prefix position, assignment as statement not expression, types are mostly not checked, etc.
- Error handling and reporting. It is wildly optimistic, enforces nothing, has only a few error checks, and will crash in spectacular and unexpected ways on the most trivial errors.
- Optimization. It treats the Xtensa CPU as a stack machine, with no attempt at register allocation, and no benefit from the sliding window. It is a major sacrifice of performance for simplicity. GCC-compiled: 16kB, 17,500 lines/s; self-compiled: 33kB, 3,900 lines/s.
- Miss a feature? Just fork it! With a working foundation in only 700 lines, it is fairly easy to get started.

## License

This is free software under MIT License, see [LICENSE](LICENSE).

## Contributing: Forks Welcome!

While I do not believe the world needs another C99 implementation, and do not intend to add features here, I am dead curious to see where the other creative minds can take a tiny self-hosting compiler on esp32.

If you organize hackathons, or assign coursework, or write tutorials, please consider xcc700 as a base to fork and extend! It can run on the available PCs, or on a $5 MCU if you want real cool hardware for the final test. Or you can port it to other systems, and use ld to link those ELF files.

## Why did I write this?

I was making an esp32 "cyberdeck", and thought it cool to build some binaries directly on it. Esp32 is underrated in userland. It can do everything a 90s PC could do and more.

You can also take this as an artistic statement, and ask yourself:
- How many Watts do you need to do fun/useful stuff on a computer?
- Isn't it nice to have simple, tinker-friendly versions of common apps?
- Do we really need 300MB mouse drivers?

Have fun!
