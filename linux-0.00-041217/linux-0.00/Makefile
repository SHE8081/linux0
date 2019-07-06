#
# if you want the ram-disk device, define this to be the
# size in blocks.
#

RAMDISK = 
AS86	=as86 -0 -a
LD86	=ld86 -0

AS	=as
LD	=ld 
LDFLAGS	=-m elf_i386 -Ttext 0 -e startup_32
CC	=gcc-3.4 -march=i386 $(RAMDISK)
CFLAGS	=-m32 -g -Wall -O2 -fomit-frame-pointer
CPP	=cpp -nostdinc -Iinclude
#若目标是.s文件，而源文件是.c文件，则使用第一个规则
#$<代表第一个先决条件，这里既是符合条件的*.c的文件
.c.s:
	$(CC) $(CFLAGS) \
	-nostdinc -Iinclude -S -o $*.s $<
#若目标是.o文件，而源文件是.s文件，则使用第一个规则
.s.o:
	$(AS) -c -o $*.o $<
#若目标是.o文件，而源文件是.c文件，则使用第一个规则
.c.o:
	$(CC) $(CFLAGS) \
	-nostdinc -Iinclude -c -o $*.o $<

all:	Image

# >是定向输出到文件，如果文件不存在，就创建文件；如果文件存在，就将其清空。
Image: boot/boot tools/system tools/build
	cp -f tools/system system.tmp
	strip system.tmp
	objcopy -O binary -R .note -R .comment system.tmp tools/kernel
	tools/build boot/boot tools/system  tools/kernel  > Image
	rm system.tmp
	rm tools/kernel -f
	sync

disk: Image
	dd bs=8192 if=Image of=/dev/fd0
	sync;sync;sync

tools/build: tools/build.c
	gcc $(CFLAGS) \
	-o tools/build tools/build.c

boot/head.o: boot/head.s
	gcc-3.4 -m32 -g -I./include -traditional -c boot/head.s
	mv head.o boot/

tools/system:	boot/head.o 
	$(LD) $(LDFLAGS) boot/head.o  -o tools/system
	  nm tools/system | grep -v '\(compiled\)\|\(\.o$$\)\|\( [aU] \)\|\(\.\.ng$$\)\|\(LASH[RL]DI\)'| sort > System.map 



boot/boot:	boot/boot.s
	$(AS86) -o boot/boot.o boot/boot.s
	$(LD86) -s -o boot/boot boot/boot.o


clean:
	rm -f Image System.map tmp_make core boot/boot 
	rm -f init/*.o tools/system tools/build boot/*.o

