
## Resources:

- Include the resources you've referred to with links. [example hyperlink](https://google.com)


***

# 1. GDB Baby Step 1

>  Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.

## Solution:

- The question itself gives us the hint on what to do, and where to find the value, we find the value in the eax register, so we just have to look at the values moved into that register


![ghidra_image_1](assets/ghidra_image_1.png)

## Flag:

```
picoCTF{549698}
```


# 2. ArmAssembly 1 

> [!NOTE]
> Curretly a WIP(too much to learn about assembly :p)
> For what argument does this program print `win` with variables 81, 0 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})

## Solution:

1. We open the arm file in an text editor.
2. 

```asm
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]
	mov	w0, 81
	str	w0, [sp, 16]
	str	wzr, [sp, 20]
	mov	w0, 3
	str	w0, [sp, 24]
	ldr	w0, [sp, 20]
	ldr	w1, [sp, 16]
	lsl	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 24]
	sdiv	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 12]
	sub	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w0, [sp, 28]
	add	sp, sp, 32
    ret 
    ```
In this we see that:
1. 32 bytes are reserved on the stack pointer
2. We store the value in the w0 register to 12th offset in the stack  // store the input arg in sp[12]
3. We move 81 to w0 register 
4. We store w0 to the 16th offset of the stack meaning we store 81 to 16th offset 
5. We store zero to 20th offset
6. move 3 to w0 
7. move w0(3) to 24th offset 
8. We load the value on 20th offset to w0
9. We load the value on 16th offset to w1
10. We left shift the value on w1 by value on w0 and store it in w0 which is 81 << 0
11. store the result of the shift on 28th offset  stack[28] = 81
12. load the value on 28th offset to w1 register w1 = 81
13. load the value on 24th offset to w0 register  w0 = 3
14. then divide w1 by w0 and store it in w0  since it is sdiv (signed
    integer division) we truncate the decimal w0 becomes 27
15. store that value on 28th offset 
16. laod that on w1 register 
17. load what was in the 12th register to w0 
18. store what was on w0 to 28th 
19. immediately load it again to w0 for some reason 
20. then we sub w1 by w0 and store result in w0. w0 = 27-w0 
21. Store w0 to 28th offset 
20. then free the stack and return from the function 

so we know 27-input = 0 is checked for success, so the input should we 27 to get the result 
so 


## Flag:

```
picoCTF{0000001B}
```

## Concepts learnt:

Basics for Arm asembly

## Notes:

- I still dont fully understnad what's happening the main function, would need to study more about stack frames and such.
  Resources:
- [Anu School of Computing Arm Cheatsheet](https://comp.anu.edu.au/courses/comp2300/resources/03-ARM-cheat-sheet/)
