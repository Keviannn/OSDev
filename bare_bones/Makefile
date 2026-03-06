TARGET = myos

OBJ = boot.o kernel.o

CC = ~/opt/cross/bin/i686-elf-gcc
AS = ~/opt/cross/bin/i686-elf-as

CFLAGS = -std=gnu99 -ffreestanding -O2 -Wall -Wextra
LFLAGS= -ffreestanding -O2 -nostdlib

all: $(TARGET)

$(TARGET): $(OBJ)
	$(CC) -T linker.ld -o $@ $(LFLAGS) $^ -lgcc

%.o: %.c
	$(CC) -c $< -o $@ $(CFLAGS) 

%.o: %.s
	$(AS) $< -o $@

clean:
	rm -f $(OBJ) $(TARGET)

