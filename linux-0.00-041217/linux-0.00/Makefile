#
# if you want the ram-disk device, define this to be the
# size in blocks.
AS86	=as86 -0 -a
LD86	=ld86 -0

AS	=gas
LD	=gld
LDFLAGS	=-s -x -M
CC	=gcc $(RAMDISK)
CFLAGS	=-Wall -O -fstrength-reduce -fomit-frame-pointer \
-fcombine-regs 
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
	tools/build boot/boot tools/system  > Image
	sync

disk: Image
	dd bs=8192 if=Image of=/dev/fd0
	sync;sync;sync

tools/build: tools/build.c
	$(CC) $(CFLAGS) \
	-o tools/build tools/build.c

boot/head.o: boot/head.s

tools/system:	boot/head.o 
	$(LD) $(LDFLAGS) boot/head.o  -o tools/system > System.map



boot/boot:	boot/boot.s
	$(AS86) -o boot/boot.o boot/boot.s
	$(LD86) -s -o boot/boot boot/boot.o


clean:
	rm -f Image System.map tmp_make core boot/boot 
	rm -f init/*.o tools/system tools/build boot/*.o

