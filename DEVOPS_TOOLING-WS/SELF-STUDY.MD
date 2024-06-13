## Network-attached storage (NAS)

Network-attached storage (NAS) is dedicated file storage that enables multiple users and heterogeneous client devices to retrieve data from centralized disk capacity. Users on a local area network (LAN) access the shared storage via a standard Ethernet connection.

NAS devices typically don't have a keyboard or display and are configured and managed with a browser-based utility. Each NAS resides on the LAN as an independent network node, defined by its own unique IP address.

The purpose of network-attached storage is to enable users to collaborate and share data more effectively. It is useful to distributed teams that need remote access or work in different time zones. NAS connects to a wireless router, making it easy for distributed workers to access files from any desktop or mobile device with a network connection. Organizations commonly deploy a NAS environment as a storage filer or the foundation for a personal or private cloud.


### Storage area networkA 

Storage Area Network (SAN) is a specialized, high-speed network that provides network access to storage devices. SANs are typically composed of hosts, switches, storage elements, and storage devices that are interconnected using a variety of technologies, topologies, and protocols. SANs may span multiple sites.

A SAN presents storage devices to a host such that the storage appears to be locally attached. This simplified presentation of storage to a host is accomplished through the use of different types of virtualization.

### Network File System (NFS)

is a distributed file system protocol originally developed by Sun Microsystems (Sun) in 1984,[1] allowing a user on a client computer to access files over a computer network much like local storage is accessed. NFS, like many other protocols, builds on the Open Network Computing Remote Procedure Call (ONC RPC) system. NFS is an open IETF standard defined in a Request for Comments (RFC), allowing anyone to implement the protocol.


### SMB: on the Windows side

SMB is primarily used in Windows environments and it forms the basis for Microsoft’s distributed file system.

IBM developed SMB originally in 1983 as a way of providing shared access to files and printers across a network. Microsoft picked it up later. The company used it in LAN Manager for IBM OS/2 in 1987 and subsequently built it into Windows NT 3.1 and has retained it in its operating systems since then.

SMB clients mount to files and directories via computer hostname or IP address (NFS uses the Unix command line). SMB can also be used in Unix and Linux environments and is a little easier to use in hybrid (Windows and Linux/Unix) environments than NFS.

### NFS versus SMB

1. NFS better for Unix/Linux, while SMB better for Windows.

2. NFS requires extra tools to support Apple, but SMB does not.

3. NFS runs in Unix/Linux and Windows; SMB needs Samba to do so.

4. NFS file locking is mandatory or advisory, but SMB locking is mandatory.

5. NFS has no fast file find; SMB has fast find.

6. NFS no server and printer browsing, while SMB has server and printer browsing.

7. NFS slower reads and writes; SMB faster.

8. NFS better with small files, while SMB fine with small files but better with large files.


### what Block-level storage is and how it is used by Cloud Service providers

block storage is referred to as block-level storage, a technology utilized for storing data in storage environments based on cloud or SANs, i.e., Storage Area Networks. For computing situations requiring safe, reliable, and efficient data transport, the developers prefer using cloud block storage.

### Block Storage Vs. File Storage

 block storage serves the purposes of business scalability, there are a few alternatives that perform some specific goals similarly. The face-off of block storage is major, with two options: object storage and file storage.

If the objective is simple, file storage stands above block storage. However, block storage devices are usually more complex and costly; they tend to be better in performance and flexibility.

In comparison to block storage, file storage comes in less cost while providing an accessible and centralized location for the file. With file storage, directories and metadata are utilized to arrange the files, which is beneficial for businesses seeking storage for large data sets.


### Block Storage Vs. Object Storage


img
Block Storage In Cloud Computing
October 26, 2022 acecloud

More often than not, block storage is referred to as block-level storage, a technology utilized for storing data in storage environments based on cloud or SANs, i.e., Storage Area Networks. For computing situations requiring safe, reliable, and efficient data transport, the developers prefer using cloud block storage.

The data is divided into blocks that are stored individually and have an associated unique identifier, and it is the SAN that allocates these blocks to the most efficient location.

This implies that the created blocks can be stored or allocated in various systems and can be configured according to the operations of different operating systems. Also, block-level storage puts data across multiple environments, leading to decoupling data from user environments.

This process makes various ways for the data resulting in quick retrieval if needed. This article will take you through the domains of cloud block storage in detail to further enhance its information. Have a read!

Advantages of block cloud storage
Here are a few benefits of block storage that result in its high demand in the market:

File Changes: In case a user performs an alteration in a file inside the block storage, the system is liable to make changes only in the block subjected to modifications. On the other hand, the entire object or file needs to be modified in case of an object or file storage.
Performance: Block storage is best suited for applications requiring low latency and high IOPS, like database servers.
Bootability of the operating system: Using a Storage Area Network, the operating systems can be booted through block storage directly.
Flexibility: With the need to increase storage, businesses can add storage blocks without compromising on the current performance level. The block servers can be shifted between the servers by altering the destination route.
Also Read: Demystifying Enhanced Cloud Reliability With Block Storage

Working on block-level storage
Contrary to object storage, the data in block storage gets stored in blocks, making the indexing and searching of data more accessible with the addition of context. On a Storage Area Network, the block can be bifurcated into various uniformly sized blocks, which results in facilitated locating the data and quick file retrieval. Based on the file retrieval request, the SAN will identify the block and present the file to the user.

Try Super-fast, Scalable & Secure Public Cloud Today
Sign Up
With SAN, one can carry out the configuration of blocks for efficient transactions and run of databases. Hence, the modified applications or blocks can be easily overwritten.

Applications like transactions and databases needing security and accessibility can have a smooth run because of the configuration ability of the blocks. Further, if you are adding new files or deleting the old ones, the prior versions will be available on the system.

Block Storage Vs. File Storage
Though block storage serves the purposes of business scalability, there are a few alternatives that perform some specific goals similarly. The face-off of block storage is major, with two options: object storage and file storage.

If the objective is simple, file storage stands above block storage. However, block storage devices are usually more complex and costly; they tend to be better in performance and flexibility.

In comparison to block storage, file storage comes in less cost while providing an accessible and centralized location for the file. With file storage, directories and metadata are utilized to arrange the files, which is beneficial for businesses seeking storage for large data sets.

For data protection and archiving, the file storage tools seem ideal with the easy arrangement and low operations costs. Inter-organization sharing of the file is also a standout feature of file storage.

The simple nature of file storage can also be its drawback. Though it follows a hierarchical arrangement, adding more files makes it challenging to sift between them. And, in terms of performance, object or block storage wins the race. A few businesses have converged block-level and file storage to benefit both technologies.

Block Storage Vs. Object Storage
Instead of creating data blocks for storage, object storage cumulates the data into an object with associated metadata. In block storage, no metadata is assigned to the blocks, meaning object storage gives more information about the data, making file customization and classification easy.

Every object has a unique identifier, which makes storing files easy. Block storage stands out to be the best when it comes to scalability, even when block storage is capable of expansion. To scale the architecture of object storage, only the addition of nodes is required in the storage object.

Though the scalability and flexibility of object storage can be in-demand, some businesses might choose performance and go for file or block cloud storage. In block storage, one can edit parts of a particular file, but the entire object needs to be edited in object storage.
