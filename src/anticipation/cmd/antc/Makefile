all: clean kernel8.img ../../../../tinygo_conf/assembly/baremetal_boot.o

# we use the bare metal linker script for the bootloader
antc: *.go
	clang --target=aarch64-elf -mno-unaligned-access -c -o ../../../../tinygo_conf/assembly/baremetal_boot.o ../../../hardware/arm-cortex-a53/barmetal_boot.S
	tinygo build -opt s -cflags='-mno-unaligned-access' -target  ../../../../tinygo_conf/rpi3.json -ldflags='../../../../tinygo_conf/assembly/baremetal_boot.o -T ../../../../tinygo_conf/rpi3.ld' -o antc .

clean:
	rm antc kernel8.img *.o >/dev/null 2>/dev/null || true

kernel8.img: antc
	llvm-objcopy -O binary antc kernel8.img


run:
	qemu-system-aarch64 -semihosting -semihosting-config enable=on,target=native,chardev=char0 \
		--chardev pty,path=/dev/ttys009,mux=on,id=char0 \
		-M raspi3\
		-kernel kernel8.img \
		-semihosting -semihosting-config enable=on,target=native,chardev=char0 \
		-serial null -serial chardev:char0

debug:
	qemu-system-aarch64 -semihosting -semihosting-config enable=on,target=native,chardev=char0 \
		--chardev pty,mux=on,id=char0 \
		-M raspi3 -S -s \
		-kernel kernel8.img \
		-semihosting -semihosting-config enable=on,target=native,chardev=char0 \
		-serial null -serial chardev:char0