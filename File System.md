# File System

A **File System** is basically a storage management method that manages how data of a storage device is organised using Files.

> A file is a basic unit of data which contains sequence of bytes. 

File System is is an ** ABSTRACTION ** of Storage devices, i.e HDD or SSD at OS level. 
At storage level data is divided into blocks of memory hence operatios work as block based, but at File System level data operations  will be byte based. 
In Linux everything is a ***File**. So File System is key in understanding for programmers or developers as the Programs will also be treated as files. 

## Key Components/features of File System:
- Storage Device 
- Device Driver
- OS Kernel
- Directory
- Superblock
- Data block
- Bitmap
- Inode(Index Node)

## Storage device : It is a component which stores data in form of one's and Zeroes. 

### Types : HDD(magnetic memory storage), SSD(electronic memory storage), USB, Cd-rom etc

- HDD : Its a magnetic storage device basically stores 1 and zero using magnetic alignment of North and South poles. It comes with huge capacity. But comparatively a little slow in reading and writing data because of the magnetic mechanism.

- SDD : Its a electronic storage device using electric charges for 1 and zero. It is speed compared to HDD but very costly.


## Why?

### Addressing problem: 

Suppose the storage device is raw and unorganised and if we want to add any data we won't know where it will go.

Also if we want to access any data from storage device without Filesystem we will have to scan entire storage device to find a file which is messy. 

File System manages data saving deleting changing effectively using File concept 

## How ?

FileSystem organises the storage device with some structure. First It divides the storage device into **BLOCKS**. Even if manufacturers provide sectors of storage device FileSystem can still manipulate block division. Such blocks are called ***Logical Blocks***.

Typically each block will be 4kb and can be customised as 1kb-8kb or higher. 
The blocks will have names too. Then FileSYstem takes some blocks., uses them for accessing other blocks. 
Some blocks will store **metadata**, some blocks will be reserved for **bitmap**. Remaining for data blocks. Using these techniques FileSystem can efficiently manage the data of Storage Device. 

## Structure:

- SuperBlock : the first block will contain metadata about the storage device itself., like type of Filesystem, Size of device,  number of blocks etc

- Bitmap: It consists of sequence of bits 1 and 0. Number of blocks = number of bits. 1 for occupied, 0 for free. 

- Inode : this contains metadata about files. For each file there will be one inode. It contains details like ownername, size, permissions (read, write, executable mode) (to change to executable mode : chmod +x filename) , time stamps like when it's last access, last modified. Some data blocks of File (12 blocks for small size files) will also be there, location of file .

- Directory: contains a table with file names and their corresponding inode numbers 
The Directory is also a special type of file. It puts the files in hierarchy. 
When a filename is used FS goes to that files inode traversing the Directory table and there by going to data block.

## Flow : 

Formatting: when a storage device is formatted all the blocks will be free. 

* When we want to create a file, The file system first checks the bitmap for free blocks, it's content will be written to those blocks and a inode will be created in storage device containing metadata about the file. Then the sequence of bits in bitmap will be updated with the number of blocks occupied. The Directory will be updated with file name and it's Inode number.

* When we want to create new file The same process FS checks for bitmap with freeblocks that's how we can avoid overwriting the same blocks with new data.

* When we want to open a file suppose cat /home/cprgrm/hello.c the FS first goes to root directory from there traverse to the filename and finds its inode number and from there it goes to the actual data block of that file and then executes the cat command

* When we want to delete a file we delete that inode pointers in Directory file table, what happens is we lose the link to our inode and thereby losing data block connection. So the actual data content in the storage device actually stays like that until it's overwritten later. 
Also when we delete a file , bitmap will be updated with free blocks list.

## System Calls and File Operations:

File operations are the core actions performed on files by the File System. Those are fopen(), fread(), fwrite(), fprintf(). Under the hood they map to System calls open, read, write, unlink(UNIX).

fopen() traverses the heirarchy path through Directory finds the inodes location of file and makes the file ready for open. 

fread() enables the File System to read a file. simliar function fgetc() only difference fgetc reads onlyone byte abd fread reads strings.

fwrite() used to write data to blocks. 
 

## Types :

Based on different OS different types of File Systems are in use. Windows uses NTFS(New Technology File System), Linux like OS use ext4, Mac use APFS. Each FS vary from one another. 

## Data Allocation: 

Two ways we can allocate Data to the datablocks. We mean Filesystem. 
- one way is to allocate Data in continuous blocks in storage device. This has drawback if we want to add(append) more data in future.
- another way is indexed allocation. That means just allocate all the freely available blocks with our content and then use a block for table containing files inode and block numbers that are allocated for that file . This can be effective for scaling but drawback is the head moves so many places to access the data

So to solve this File system simply tells OS I will allocate freely available blocks as I get but they should be seen as continuous allocations that is by moving all the blocks together after allocation.

## Buffering, Caching :

For effectiveness, when a file is opened, the OS loads large memory blocks into RAM segments at once even if we want to open one byte of a file. 
Sometimes OS loads the next block in series from the current block being opened will be loaded into RAM segment, that is called CACHING

While writing data to HDD, time will be more so OS simply writes the data to a BUFFER segment in RAM first then they will be flushed to Harddisk all at once.

For better efficiency, File System sometimes does a trade off between Speed and Consistency (correctness)

## Drivers and File System :

Since the File System is an abstraction of the hardware storage device, it needs some interface to connect with those devices. Drivers are modules that act as bridge between File Systema and the Hardware device. 

- The File System translates high level file paths and byte based content to block based requests, the Drivers converts them into Hardware commands.

### Sample C program workflow :

- **CODE** :
#include <stdio.h>

int main()
{

	FILE *file_pointer = fopen("D:/AI Karyashala Rough/cpractice/hello.txt","w");
	if (file_pointer == NULL)
	{
		printf("Failed to open");
		return 1;
	}
	fclose(file_pointer);
	printf("Successfully opened the file");
	return 0;
}

- This is a C program which when and compiled produces a binary file stored as executable.

what happens when we run ./fileopen from the cwd:

- the file will be loaded to RAM and the ip sets to main() of the C program
- when the instruction reaches fopen() it gets mapped to Syscall **OPEN()** , CPU activates Kernel
- the File System traverse the path understands the hierarchy looks for Directory --> Inode numbers(metadata) --> file blocks --> data block. 
- if the file exists it finds the inode details via directory and reaches the file path and makes it ready to be opened either as read or write, returns the file descriptor value to the file pointer of type FILE which contains file information.
- if file dont exist and is opened as read mode the fail doesnot open and output will be failed to open
- if file doesnt exist and is opened as write mode the Kernel sends I/O request to the disk via disk driver and waits for the disk response. 
-  in this meantime CPU handles other process
- after the **SYSCALL** returns value successfull as fd = 3; the CPU turns back from **Kernel** mode to CPU user mode and continue with program/application execution.
- similarly when execution reaches floce(); the syscall close() gets called and OS again switch on Kernel mode and Kernel takes control of the process. when the function call returns value the cpu turn back to user mode to continue with process  execution., until the return 0 is reached. 

** during this process few other things happen parallel, when reading a byte from file, the block containg the byte will be loaded to RAM segment and additionally, the next block in the sequence will be loaded into cache segment. this is called prefetching data blocks to reduce I/O requests to disk as they are slow in process.

** while writing data to file(disk), Kernel writes the data to a buffer memory segment first and only writes to disk either when the flcose() functions is called or blocks filled or we use function like fflush()

<<<<<<< HEAD
## FlowChart :

┌──────────────────────────────────────────────────────────┐
│ 1. You type command in Terminal (User Action)            │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Keyboard hardware detects keypress                       │
│  → sends interrupt to CPU via keyboard driver             │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Kernel (Interrupt Handler) runs in Kernel Mode            │
│  → updates input buffer                                   │
│  → sends data to terminal process                         │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Shell (User Mode process) receives your command string    │
│ Example: ./a.out                                          │
│ Parses and recognizes executable to run                   │
│ Calls execve() system call                                │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ SYSTEM CALL: execve() → CPU switches to Kernel Mode       │
│  - Kernel loads program binary into RAM                   │
│  - Sets up code, data, stack, heap segments               │
│  - Initializes file descriptors:                          │
│       fd=0 (stdin), fd=1 (stdout), fd=2 (stderr)          │
│  - Sets instruction pointer → main()                      │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ CPU now in User Mode executing main()                     │
│ fopen("data.txt", "w") called → inside libc               │
│ libc → triggers open() system call                        │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ SYSTEM CALL: open() → switch to Kernel Mode               │
│  - Kernel’s VFS receives request                          │
│  - Parses path, traverses directory tree using dentry &   │
│    inode caches                                           │
│  - If file doesn’t exist and mode is "w", creates inode   │
│  - Allocates file object and assigns fd=3                 │
│  - Returns fd=3 to user process                           │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Back to User Mode                                         │
│ FILE *f now wraps fd=3 (through stdio buffering)           │
│ fprintf(f, "Hello, File System!")                         │
│ → writes into user-space buffer                           │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ fflush(f) or buffer full → triggers write() syscall       │
│ SYSTEM CALL: write()                                      │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Kernel Mode:                                              │
│  - Copies data from user buffer → kernel page cache        │
│  - Marks page as “dirty”                                   │
│  - Returns success immediately (no disk write yet!)        │
│  - File write cached in memory                             │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Kernel write-back daemon (runs in background)             │
│  - Groups dirty pages                                      │
│  - Converts them into block I/O requests (BIOs)            │
│  - Filesystem driver formats metadata + journal entries    │
│  - Sends BIOs to block layer                               │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Block Layer + I/O Scheduler                               │
│  - Merges and reorders writes for efficiency               │
│  - Passes to disk driver (e.g., SATA, NVMe)                │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Disk Driver + DMA                                         │
│  - Driver configures DMA controller                        │
│  - CPU hands control → DMA copies data from RAM → disk     │
│  - Disk firmware writes data to platter/flash sectors      │
│  - On completion, disk raises interrupt                    │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Kernel interrupt handler                                  │
│  - Marks page as “clean” in page cache                     │
│  - Updates inode metadata (mtime, size)                    │
│  - If journaling: commits transaction                      │
│  - Notifies waiting processes (if any)                     │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Control returns to User Mode process                      │
│ fclose(f) → close() syscall → kernel closes fd=3           │
│ Kernel releases file object and decrements inode refcount  │
│ If no references remain, inode may be evicted from cache   │
└──────────────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────┐
│ Program exits → exit() syscall                            │
│ Kernel cleans up process resources                        │
│ Shell regains control and prints prompt                   │
└──────────────────────────────────────────────────────────┘

=======
>>>>>>> bf9f1aeacd3c7d49f87feb166f53683ef232ddff

This are basic details about **FILE SYSTEM** in Computer Knowledge. 





