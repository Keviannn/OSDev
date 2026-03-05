# Bare Bones
This tutorial follows the creation of a simple 32-bit x86 kernel and boot it.

This is just notes and implementation, the real deal is on [OSDev.org](https://wiki.osdev.org/Bare_Bones) if you happen to find this before.

## Notes
Is important to create a [cross compiler](https://wiki.osdev.org/GCC_Cross-Compiler) to compile in 32 bits.

To assemble: `i686-elf-as boot.s -o boot.o`\
To compile: `i686-elf-gcc -c kernel.c -o kernel.o -std=gnu99 -ffreestanding -O2 -Wall -Wextra`\
To link: `i686-elf-gcc -T linker.ld -o myos -ffreestanding -O2 -nostdlib boot.o kernel.o -lgcc`\
As we are in a Freestanding version, we have to create our own linker scrip (`linker.ld`).\
To check valid: `grub-file --is-x86-multiboot myos` (silent so we have to check exit status)\
Check valid script:
```bash
if grub-file --is-x86-multiboot myos; then
  echo multiboot confirmed
else
  echo the file is not multiboot
fi
```
To create a CD-ROM image (xorriso needed):
1. Create file grub.cfg:
```
menuentry "myos" {
	multiboot /boot/myos
}
```
2. Create the bootable image:
```bash
mkdir -p isodir/boot/grub
cp myos isodir/boot/myos
cp grub.cfg isodir/boot/grub/grub.cfg
grub-mkrescue -o myos.iso isodir
```

Run kernel:
1. With the iso: `qemu-system-i386 -cdrom myos.iso`
2. With the binary: `qemu-system-i386 -kernel myos`

## Continuing
**Adding Support for Newlines to Terminal Driver**

The current terminal driver does not handle newlines. The VGA text mode font stores another character at the location, since newlines are never meant to be actually rendered: they are logical entities. Rather, in terminal_putchar check if c == '\n' and increment terminal_row and reset terminal_column.

**Implementing Terminal Scrolling**

In case the terminal is filled up, it will just go back to the top of the screen. This is unacceptable for normal use. Instead, it should move all rows up one row and discard the upper most, and leave a blank row at the bottom ready to be filled up with characters. Implement this.

**Rendering Colorful ASCII Art**

Use the existing terminal driver to render some pretty stuff in all the glorious 16 colors you have available. Note that only 8 colors may be available for the background color, as the uppermost bit in the entries by default means something other than background color. You'll need a real VGA driver to fix this. 


