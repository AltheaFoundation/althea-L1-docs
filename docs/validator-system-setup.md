# System setup

While Althea L1 full nodes can be run on any platform it is recommended you run your validator on Linux. The most up to date version of any particular distribution is recommended.

## Recomended specs

The recommended specs are 8gb ram and a quad core CPU. Storage requirements will change over time as the chain grows, but it is recommended to start with a SSD with least 125gb of free space. Note that if you are not using a Copy-on-write (CoW) filesystem, you will need enough additional storage to keep a backup of the entire blockchain during upgrades.

### Optional: Copy on write + compressing filesystem

This entire section is optional, we do not recommend this if you are new to Linux, using a cloud server, or unfamiliar with disk formatting.

Linux supports two major CoW filesystems with transparent compression: ZFS and BTRFS. If you have the experience to do so, we recommend formatting the disk that will be storing your blockchain data with one of these two.

Modern compression algorithms like [ZSTD](https://github.com/facebook/zstd) will reduce the disk space required for blockchain storage by 25% or more. Furthermore since your disk has to read and write 25% less data disk i/o actually improves. The trade-off is minimal additional CPU usage, and is by far worth it for a disk i/o bound application like a Cosmos full node.

Furthermore, both of these are Copy-on-write (CoW) systems. When you "copy" a folder instead of actually duplicating all that data on disk a virtual copy is made and two folders are shown. When you edit any files in the first folder a copy is then made only of the file you edited or 'wrote' to. This means you can instantly make a backup of a 200gb blockchain data folder with only seconds of downtime and without needing enough disk space to store two entire copies of the blockchain.

#### BTRFS

For BTRFS simply format at data partiton and select 'BTRFS' as the filesystem. You may need to install the "btrfs-progs" package for your distribution. Edit the mount options to include "-o compress=zstd" and reboot.

#### ZFS

While BTRFS is very easy to get started with and provides our two key advantages (compression and copy on write) it very quickly requires expert advice, as you get into more advanced multi-disk configurations. If you want to have reundant disks we **strongly recommend** ZFS over BTRFS. [ZFS guide](https://openzfs.github.io/openzfs-docs/Getting%20Started/index.html)
