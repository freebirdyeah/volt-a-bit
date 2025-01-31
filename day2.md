# Volt-A-Bit

## MEET-2

### Goal: Get a blinking cursor in qemu-i386 (successful boot)

Here's how we boot up.

1. BIOS checks the bootable devices for magic bytes (boot signature): `0xaa55` which are the last two bytes in the 512 bytes. 
2. loads into memory at `0x0000:0x7c00`


SOME CLARIFICATIONS + RESOURCES:-

1. MBR -> Master Boot Record, is the first boot sector of a disk.
2. int 10h -> wikipedia page
3. bios interrupt call -> wikipedia page: I/O stuff, abstractions. BIOS will worry bro you just code.

An Important line:-

The BIOS maintains separate cursor positions for up to eight text display pages and provides for TTY-like output with automatic line wrap and interpretation of basic control characters such as carriage return and line feed


## The first version of our bootloader

```asm

bits 16
org 0x7c00


start:
      	mov ah, 0x0e 
        mov al, 'X'
	int 0x10
	jmp $

times 510 - ($ - $$) db 0
dw 0xaa55

```

### Explanations

1. `bits 16` --> specify 16 bit asm code
2. `org 0x7c00` --> specify the memory address to load at
3. `mov ah, 0x0e` --> BIOS interrupt to write character
4. `mov al, 'X'` --> moves the character value to `al`
5. `int 0x10` --> calls the interrupt 


Question: why does the interrupt go in `ah` and char go in `al` --> just design dude

💡 BIOS follows this convention for consistency:

AH = Service number (what you want BIOS to do).
AL (or other registers) = Input/output data.


6. `jmp $` --> every instruction has some memory address. We basically keep coming here for an infinite loop
7. `times 510 - ($ - $$) db 0`: $, $$ --> current address, starting address.
    times: do operation a certain number of times
    `510 - ($-$$)` basically gets the number of unused bytes (511, 512) --> magic bytes
    `db 0` --> declares each byte to 0


commands to run:-

1.`nasm -f bin boot.s -o boot.bin`
2. `qemu-system-i386 -drive format=raw,file=boot.bin`
