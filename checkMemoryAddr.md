There are many questions about variable locations in memory layout, especially about string literals/char arrays.
In fact, there are ways to speculate memory locations, and here is a mini tutorial I made how how to use gcc, objdump and gdb to do that.
(The following demo is performed on linux. Please be aware that `objdump` program has different interface on MacOS. )

Let's start from a simple piece of code

```c
#include <string.h>
#include <stdio.h>

const char* greeting = "hello 61c";
const int big_num = 23333;

int main () {
   char *str0 = "Roses are red, ";
   char str1[] = "Violets are blue. ";
   str1[0] = 'X';
   printf("%s\n, %s\n", str0, str1);
   char* str2 = "gcc is fun, ";
   char* str3 = "gdb is too. ";
   printf("%s\n, %s\n", str2, str3);
   return 0;
}
```

Copy-paste this code snippet to a file named demo.c, then compile it with
```console
$ gcc -O0 -g -o demo demo.c
```
On executing this command, `gcc`, the compiler, would compile and link the code into an object file named `demo`.
Now let's take a look at the section info using `objdump`. The command `objdump -h demo` I ran below is basically saying "showing me headers of all sections".

```console
$ objdump -h demo

demo:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .interp       0000001c  00000000004002a8  00000000004002a8  000002a8  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .note.gnu.build-id 00000024  00000000004002c4  00000000004002c4  000002c4  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  2 .note.ABI-tag 00000020  00000000004002e8  00000000004002e8  000002e8  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  3 .hash         00000024  0000000000400308  0000000000400308  00000308  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .gnu.hash     0000001c  0000000000400330  0000000000400330  00000330  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  5 .dynsym       00000060  0000000000400350  0000000000400350  00000350  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  6 .dynstr       0000003f  00000000004003b0  00000000004003b0  000003b0  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  7 .gnu.version  00000008  00000000004003f0  00000000004003f0  000003f0  2**1
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  8 .gnu.version_r 00000020  00000000004003f8  00000000004003f8  000003f8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  9 .rela.dyn     00000030  0000000000400418  0000000000400418  00000418  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 10 .rela.plt     00000018  0000000000400448  0000000000400448  00000448  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 11 .init         00000017  0000000000401000  0000000000401000  00001000  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 12 .plt          00000020  0000000000401020  0000000000401020  00001020  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 13 .text         000001c1  0000000000401040  0000000000401040  00001040  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 14 .fini         00000009  0000000000401204  0000000000401204  00001204  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 15 .rodata       00000047  0000000000402000  0000000000402000  00002000  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 16 .eh_frame_hdr 00000034  0000000000402048  0000000000402048  00002048  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 17 .eh_frame     000000f0  0000000000402080  0000000000402080  00002080  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 18 .init_array   00000008  0000000000403e00  0000000000403e00  00002e00  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 19 .fini_array   00000008  0000000000403e08  0000000000403e08  00002e08  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 20 .dynamic      000001e0  0000000000403e10  0000000000403e10  00002e10  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 21 .got          00000010  0000000000403ff0  0000000000403ff0  00002ff0  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 22 .got.plt      00000020  0000000000404000  0000000000404000  00003000  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 23 .data         00000018  0000000000404020  0000000000404020  00003020  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 24 .bss          00000008  0000000000404038  0000000000404038  00003038  2**0
                  ALLOC
 25 .comment      00000080  0000000000000000  0000000000000000  00003038  2**0
                  CONTENTS, READONLY
 26 .debug_aranges 00000130  0000000000000000  0000000000000000  000030c0  2**4
                  CONTENTS, READONLY, DEBUGGING
 27 .debug_info   0000060c  0000000000000000  0000000000000000  000031f0  2**0
                  CONTENTS, READONLY, DEBUGGING
 28 .debug_abbrev 00000297  0000000000000000  0000000000000000  000037fc  2**0
                  CONTENTS, READONLY, DEBUGGING
 29 .debug_line   00000349  0000000000000000  0000000000000000  00003a93  2**0
                  CONTENTS, READONLY, DEBUGGING
 30 .debug_str    0000067b  0000000000000000  0000000000000000  00003ddc  2**0
                  CONTENTS, READONLY, DEBUGGING
 31 .debug_loc    0000016f  0000000000000000  0000000000000000  00004457  2**0
                  CONTENTS, READONLY, DEBUGGING
 32 .debug_ranges 00000080  0000000000000000  0000000000000000  000045d0  2**4
                  CONTENTS, READONLY, DEBUGGING

```
Don't be surprised at the many sections, but just in case you wonder, wiki has a good introduction of data sections: [Data Segment](https://en.wikipedia.org/wiki/Data_segment).
(In fact, the memory layout model showed in class is the memory layout of a running program, while what you see here is the part that's "static".)
Now that you know the existence of each section, let's check out their address using `-j` flag followed by a section name. For example:
```console
$ objdump -s -j.data demo

demo:     file format elf64-x86-64

Contents of section .data:
 404020 00000000 00000000 00000000 00000000  ................
 404030 04204000 00000000                    . @.....
```
The left most column under `Contents of section .data` denotes the memory address associated with content in this section.
Another example:
```console
$ objdump -s -j.text demo

demo:     file format elf64-x86-64

Contents of section .text:
 401040 31ed4989 d15e4889 e24883e4 f0505449  1.I..^H..H...PTI
 401050 c7c00012 400048c7 c1a01140 0048c7c7  ....@.H....@.H..
 401060 12114000 ff15862f 0000f40f 1f440000  ..@..../.....D..
 401070 b8384040 00483d38 40400074 13b80000  .8@@.H=8@@.t....
 401080 00004885 c07409bf 38404000 ffe06690  ..H..t..8@@...f.
 401090 c366662e 0f1f8400 00000000 0f1f4000  .ff...........@.
 4010a0 be384040 004881ee 38404000 4889f048  .8@@.H..8@@.H..H
 4010b0 c1ee3f48 c1f80348 01c648d1 fe7411b8  ..?H...H..H..t..
 4010c0 00000000 4885c074 07bf3840 4000ffe0  ....H..t..8@@...
 4010d0 c366662e 0f1f8400 00000000 0f1f4000  .ff...........@.
 4010e0 803d512f 00000075 17554889 e5e87eff  .=Q/...u.UH...~.
 4010f0 ffffc605 3f2f0000 015dc30f 1f440000  ....?/...]...D..
 401100 c366662e 0f1f8400 00000000 0f1f4000  .ff...........@.
 401110 eb8e5548 89e54883 ec3048c7 45f81420  ..UH..H..0H.E..
 401120 400048b8 56696f6c 65747320 48ba6172  @.H.Violets H.ar
 401130 6520626c 75654889 45d04889 55d866c7  e blueH.E.H.U.f.
 401140 45e02e20 c645e200 c645d058 488d55d0  E.. .E...E.XH.U.
 401150 488b45f8 4889c6bf 24204000 b8000000  H.E.H...$ @.....
 401160 00e8cafe ffff48c7 45f02d20 400048c7  ......H.E.- @.H.
 401170 45e83a20 4000488b 55e8488b 45f04889  E.: @.H.U.H.E.H.
 401180 c6bf2420 4000b800 000000e8 a0feffff  ..$ @...........
 401190 b8000000 00c9c366 0f1f8400 00000000  .......f........
 4011a0 41574c8d 3d572c00 00415649 89d64155  AWL.=W,..AVI..AU
 4011b0 4989f541 544189fc 55488d2d 482c0000  I..ATA..UH.-H,..
 4011c0 534c29fd 4883ec08 e833feff ff48c1fd  SL).H....3...H..
 4011d0 03741b31 db0f1f00 4c89f24c 89ee4489  .t.1....L..L..D.
 4011e0 e741ff14 df4883c3 014839dd 75ea4883  .A...H...H9.u.H.
 4011f0 c4085b5d 415c415d 415e415f c30f1f00  ..[]A\A]A^A_....
 401200 c3
```

With enough knowledge about static section information, let's get the programming running and ask gdb where any given variable resides in the runtime memory layout.
```console
$ gdb ./demo
```
(I'm personally not a big fan of cgdb.. there are a number of better command-line debuggers around if you need more fancy features, otherwise gdb itself probably suffices.)
The screenshot below records what command I executed and what I got. The way you ask about the address of a certain variable is really the same in gdb as in C. Take a look and see what insights you can draw from these addresses that gdb printed out.
![](https://i.imgur.com/rqwk1wx.png)

Global variables `greeting` and `big_num` have a very small address(only six hex digits). Notice that they are both in the range of `.data` section that `objdump` showed. Therefore, you know these two global variables are located in the static section(using that langauge we customized for 61c).
Practially, you can pretty much safely assume any address starting from `0x7fff...` is an address on stack holding a very high position, which corresponds to a large number, on the memory layout.
Therefore, you can immediately conclude that the two `char*` vaiables `str0`(at `0x7fffffffde18`) and `str1`(at `0x7fffffffddf0`) are sitting on stack.

If you checkout the address of a character within string greeting(screenshot below), you may notice that this should be an address in `.rodata` section according to objdump. So as you may recall from lectures, string literals are stored  read-only data in a C program. Therefore, I can conclude that global variable `greeting` is located in the `.data` section. It contains the address to a character which is located in the `.rodata` section. Both `.data` and `.rodata` belong to the data section we refer to in 61c.

![](https://i.imgur.com/oAykY6K.png)

-- As a practice, can you create something on heap and verify its address?


In conclusion, you can use this little trick whenever you are concerned about memory location of anything in a C program. Hope it helps.
