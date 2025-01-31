# Day 3

## Tasks
1. Make a new file and print `"Hello, World"` along with the blinking cursor.
2. search about makefiles.


## Let's make a better bootloader to print string.

``` asm
bits 16
org 0x7c00


start:
	message: db "Hello, World!", 0
	mov si, message 	     ; si points to the start of the string


print_func:
	mov al, [si]		     ; moves the first char into memory
	cmp al, 0
	je done

	mov ah, 0x0e 		     ; interrupt to print
	int 0x10		         ; calls the interrupt

	inc si					 ; dont do inc al! we need to increment the pointer to the string so inc si.
	jmp print_func

done:
	jmp $


times 510 - ($ - $$) db 0
dw 0xaa55

```

### Explanations

1. declaring our string
2. moving the beginning address of the string to si register
3. moving the first character value into al by dereferencing the pointer to the first char
4. comparing to 0, if null byte then jumps to done else continues 
5. specifies the interrupt
6. calls the interrupt to print
7. increments the pointer, pointing to the next char
8. jmps to start if null bytes not there
9. jmp $ -> inf loop    



## Makefile


```
ASM_DIR:=asm
OUT_DIR:=out

default: bootloader run

bootloader:
	nasm -f bin $(ASM_DIR)/boot.asm -o $(OUT_DIR)/hello.bin
.PHONY: bootloader

run:
	qemu-system-i386 -drive format=raw,file=$(OUT_DIR)/hello.bin
.PHONY: run
```
