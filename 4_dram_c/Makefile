CFLAGS := -nostdlib -O0
TARGET := dram.bin
$(TARGET) : start.o lowlevel_init.o mem_init.o main.o link.lds
	$(CROSS_COMPILE)ld -T link.lds -o xxx.elf $^
	$(CROSS_COMPILE)objcopy -O binary xxx.elf $@
	$(CROSS_COMPILE)objdump -D xxx.elf > xxx.dis
	$(CROSS_COMPILE)objdump -b binary -D -m arm $@ > b.dis

%.o : %.S
	$(CROSS_COMPILE)gcc -o $@ $< -c

%.o : %.c
	$(CROSS_COMPILE)gcc $(CFLAGS) -o $@ $< -c

%.lds: %.lds.S
	$(CROSS_COMPILE)gcc -E -P $< -o $@
.PHONY:clean flash putty
clean:
	rm -rf *.o *.elf *.bin *.dis mk4412

flash:
	gcc ../tools/mk4412.c -static -o mk4412
	./mk4412 $(TARGET)
	@if [ -b /dev/sdb ]; then \
	sudo mkfs.vfat -F 32 -I /dev/sdb; \
	sudo dd if=/dev/zero of=/dev/sdb bs=512 seek=1 iflag=dsync oflag=dsync count=2048; \
	sudo dd if=./$(TARGET) of=/dev/sdb bs=512 seek=1 iflag=dsync oflag=dsync; \
	fi
