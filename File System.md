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

- Storage device is a component which stores data in form of one's and Zeroes. 

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

## System Calls:

When we open, read, write a file what happens under hood at system level is the system call functions fopen(), fwrite(), fread() will be called in the cpu. 

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

This is basic details about **FILE SYSTEM** in Computer Knowledge. 





