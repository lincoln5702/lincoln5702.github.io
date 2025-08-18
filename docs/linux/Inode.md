# Inode in Linux

Each partition in a filesystem contains an **inode table**, which stores information about files and directories. In the previous *Boot Process of Linux* document, I briefly mentioned partitions and their components. Here, we will focus more deeply on **inodes** and related concepts.

One of the functions of a **superblock** is to store the size of the inode table. The superblock also contains the location of the inode for the root (`/`) directory.

An **inode table** typically contains the following information:

- File type (directory, file, symbolic link)  
- Owner (user ID / UID)  
- Group (group ID / GID)  
- Access permissions (owner, group, others)  
- Timestamps (last access, modification, etc.)  
- Number of hard links to the file  
- File size in bytes  
- Pointers to the data blocks of the file  

![](attachments/Inode_one.png)

---

### In-depth Understanding of Inodes
Let’s take the above figure as an example to analyze inodes in depth. Here, we are looking at the inode entry of the `/etc/passwd` file. This file stores user login information, such as username, UID, GID, and login shell.

To reach `/etc/passwd`, the root directory (`/`) must be accessed first. The root directory has its own inode entry, determined from the superblock. The inode entry for `/` stores its permissions, type, UID, and GID. Using this information, the system determines whether access is allowed.  

From there, `/` (which is a directory) contains a pointer to `/etc` (inode 7 in the figure). `/etc` in turn contains a pointer to `passwd` (inode 6422). Finally, using the pointer for `passwd`, the system locates the `/etc/passwd` file.

**Important:** An inode does **not** store the names of directories or files.  

---

### Understanding Directory Files
Directories are special files that act as containers, holding access paths to other files in the filesystem. A directory entry typically contains:  

- The directory name  
- The inode number  
- The length of the directory entry (in bytes)  

The figure above shows directory entries for `/etc` and `/`.  

[Reference link](https://tldp.org/LDP/tlk/fs/filesystem.html#tth_sEc9.1.4)

---

### Hard Links
Each file or directory in Linux has a name stored in the directory entry, along with its inode number. Since inodes do not store file names, we can create multiple names (links) for the same inode. These are known as **hard links**.

A hard link points directly to the same inode entry. For example, if we create a hard link named `bar` for a file named `foo`, both `foo` and `bar` will point to the same inode. If one file is deleted, the other continues to exist because the inode still has references. The **link count** increases when hard links are created.

**Limitations of hard links:**
- Cannot be created for directories (to avoid circular references).  
- Work only within a single filesystem, since inode numbers are unique only within that filesystem.  

![](attachments/Inode_2.png)

#### In-depth Example with an Inode Table
In the figure above, we have a file named `this`. A hard link is created with the name `that`. Both `this` and `that` point to the same inode (61), which points to the file’s actual data blocks.

---

### Symbolic (Soft) Links
A **symbolic link** (or soft link) works differently. Instead of pointing to an inode directly, a symbolic link stores the **name of the target file**.

![](Inode_3.png)

Breaking down the figure:  
We create a symbolic link called `other`, pointing to `/home/kiran/other`. This path resolves to inode 309, which then points to `/home/erena/this`. Here, `other` is a symbolic link to the name `this`.  

Key differences compared to hard links:
- The **link count does not increase** when creating a symbolic link.  
- Symbolic links overcome the limitations of hard links:  
  - They can be created for directories.  
  - They can span multiple filesystems.  

---

### Where Are They Used?
- **Hard links**: Useful for backup and recovery. By creating multiple links to important files, accidental deletion can be avoided. They are also space-efficient, since multiple filenames can point to the same data blocks.  
- **Soft links**: Commonly used as shortcuts to files or directories. They can also reference files across different filesystems.  

---

### Creating Links
The `ln` command is used to create both hard and soft links.  


### Creating a hard link
````
ln /path/to/original/file /path/to/hardlink

````

### Creating a soft (symbolic) link
````
ln -s /path/to/original/file /path/to/softlink
````
