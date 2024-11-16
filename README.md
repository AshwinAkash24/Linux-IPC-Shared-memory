# Ex06-Linux IPC-Shared-memory
# Name: Ashwin Akash M
# Reference Number:212223230024
# Department: Artificial Intelligence And Data Science
# AIM:
To Write a C program that illustrates two processes communicating using shared memory.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Shared Memory

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that illustrates two processes communicating using shared memory.
## shm.c
```
#include<unistd.h> 
#include<stdlib.h> 
#include<stdio.h> 
#include<string.h> 
#include<sys/shm.h>
#define TEXT_SZ 2048 
struct shared_use_st
{
int written_by_you;
char some_text[TEXT_SZ];
};
int main()
{
int running = 1;
void *shared_memory = (void *)0; 
struct shared_use_st *shared_stuff; 
int shmid;
srand( (unsigned int)getpid() ); 
shmid = shmget( (key_t)1234, sizeof(struct shared_use_st), 0666 |IPC_CREAT );
printf("Shared memory id is %d \n",shmid);
if (shmid == -1)
{
fprintf(stderr, "shmget failed\n");
exit(EXIT_FAILURE);
}
shared_memory = shmat(shmid,(void *)0, 0); if (shared_memory == (void *)-1)
{
fprintf(stderr,	"shmat	failed\n"); exit(EXIT_FAILURE);
}
printf("Memory Attached at %x\n", (int)shared_memory);
shared_stuff = (struct shared_use_st *) shared_memory;
shared_stuff->written_by_you	=	0;
 while(running)
{
if(shared_stuff->written_by_you)
{
printf("You Wrote: %s", shared_stuff->some_text);
sleep( rand() %4 );
shared_stuff->written_by_you = 0;
if (strncmp(shared_stuff->some_text, "end", 3)== 0){
running = 0;}}
}
if (shmdt(shared_memory) == -1)

{
fprintf(stderr, "shmdt failed\n"); exit(EXIT_FAILURE);
}
if (shmctl(shmid, IPC_RMID, 0) == -1)
{
fprintf(stderr, "failed to delete\n");
exit(EXIT_FAILURE);
} exit(EXIT_SUCCESS);
}
```

## shm1.c
```
#include<unistd.h> 
#include<stdlib.h> 
#include<stdio.h> 
#include<string.h>
#include<sys/shm.h>
#define TEXT_SZ 2048 
struct shared_use_st{
int written_by_you;
char some_text[TEXT_SZ];
};
int main()
{
int running =1;
void *shared_memory = (void *)0; 
struct shared_use_st *shared_stuff; 
char buffer[BUFSIZ];
int shmid;
shmid	=shmget(	(key_t)1234,	sizeof(struct shared_use_st), 0666 | IPC_CREAT);
printf("Shared memory id = %d \n",shmid);
if (shmid == -1)
{
fprintf(stderr, "shmget failed\n"); exit(EXIT_FAILURE);
}
shared_memory=shmat(shmid, (void *)0, 0);
if (shared_memory == (void *)-1){
fprintf(stderr,	"shmat	failed\n"); exit(EXIT_FAILURE);}
printf("Memory Attached at %x\n", (int) shared_memory); 
shared_stuff = (struct shared_use_st *)shared_memory; 
while(running)
{
while(shared_stuff->written_by_you== 1)
{
sleep(1);
printf("waiting for client.	\n");
}
printf("Enter Some Text: "); fgets (buffer, BUFSIZ, stdin);
strncpy(shared_stuff->some_text, buffer, TEXT_SZ);
shared_stuff->written_by_you = 1;
if(strncmp(buffer, "end", 3) == 0){
running = 0;}}
if (shmdt(shared_memory) == -1)
{
fprintf(stderr, "shmdt failed\n"); exit(EXIT_FAILURE);
} exit(EXIT_SUCCESS);
}
```
## OUTPUT
### ./shm.o
![Screenshot from 2024-11-16 13-20-05](https://github.com/user-attachments/assets/6bc977ca-6897-4372-8975-a72caf4abc06)

### ./shm1.o
![Screenshot from 2024-11-16 13-20-21](https://github.com/user-attachments/assets/f48eaa8f-180a-4dc4-b0a1-d45fca8297e2)
### ipcs
![Screenshot from 2024-11-16 13-22-03](https://github.com/user-attachments/assets/64f6980e-729e-4d8d-94f4-fbc9936c3be2)


# RESULT:
The program is executed successfully.
