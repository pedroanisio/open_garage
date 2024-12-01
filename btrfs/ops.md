### RAID10 on BTRFS

- **Prepare Disks**
  ```shell
  wipefs -a /dev/sda
  wipefs -a /dev/sdb
  wipefs -a /dev/sdc
  wipefs -a /dev/sdd
  ```

- **Create RAID10**
  ```shell
  mkfs.btrfs -d raid10 -m raid10 /dev/sda /dev/sdb /dev/sdc /dev/sdd -f
  ```

- **Mount Filesystem**
  ```shell
  mkdir /mnt/btrfs
  mount /dev/sda /mnt/btrfs
  ```

- **Display Filesystem Information**
  ```shell
  btrfs filesystem show /mnt/btrfs
  ```

- **Configure Filesystem Mount on Boot**
  ```shell
  UUID=$(blkid -s UUID -o value /dev/sda) && echo "UUID=$UUID /mnt/btrfs btrfs defaults 0 0" | tee -a /etc/fstab
  systemctl daemon-reload 
  mount -a
  ```

- **Run Disk Performance Tests**
  ```shell
  fio --name=write_test --directory=/mnt/btrfs --ioengine=libaio --rw=write --bs=1m --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting
  fio --name=read_test --directory=/mnt/btrfs --ioengine=libaio --rw=read --bs=1m --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting
  fio --name=rand_write_test --directory=/mnt/btrfs --ioengine=libaio --rw=randwrite --bs=4k --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting
  fio --name=rand_read_test --directory=/mnt/btrfs --ioengine=libaio --rw=randread --bs=4k --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting
  ```

- **Save Test Results to JSON**
  ```shell
  fio --name=write_test --directory=/mnt/btrfs --ioengine=libaio --rw=write --bs=1m --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting --output=write_test.json --output-format=json
  fio --name=read_test --directory=/mnt/btrfs --ioengine=libaio --rw=read --bs=1m --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting --output=read_test.json --output-format=json
  fio --name=rand_write_test --directory=/mnt/btrfs --ioengine=libaio --rw=randwrite --bs=4k --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting --output=rand_write_test.json --output-format=json
  fio --name=rand_read_test --directory=/mnt/btrfs --ioengine=libaio --rw=randread --bs=4k --direct=1 --size=10G --numjobs=4 --runtime=60 --group_reporting --output=rand_read_test.json --output-format=json
  ```

### Rebalance and Maintenance

- **Rebalance the Filesystem**
  ```bash
  btrfs balance start /mnt/btrfs
  ```
  ```bash
  btrfs balance status /mnt/btrfs
  ```

- **Check Filesystem Usage**
  ```bash
  btrfs filesystem df /mnt/btrfs
  btrfs filesystem usage /mnt/btrfs
  ```

- **Convert Metadata and Data**
  ```bash
  btrfs balance start -mconvert=raid1c3 -dconvert=raid1 /mnt/btrfs
  ```

### Storage Expansion Options

#### Option 1: Adding a 500GB NVMe

1. **Install Bcache Tools**
   ```bash
   apt install bcache-tools
   ```

2. **Set Up NVMe as Bcache Cache**
   ```bash
   make-bcache -B /dev/sda -C /dev/nvme0n1
   ```

3. **Format and Mount Bcache Device**
   ```bash
   mkfs.btrfs /dev/bcache0
   mount /dev/bcache0 /mnt/btrfs
   ```

4. **Add Existing Devices to Bcache**
   ```bash
   btrfs device add /dev/sdb /mnt/btrfs
   btrfs device add /dev/sdc /mnt/btrfs
   btrfs device add /dev/sdd /mnt/btrfs
   ```

#### Option 2: Adding a 4TB HDD

1. **Add the HDD to the BTRFS Array**
   ```bash
   btrfs device add /dev/sdf /mnt/btrfs
   ```

2. **Rebalance the Filesystem**
   ```bash
   btrfs balance start /mnt/btrfs
   ```

### Check Device Stats

- **Device Statistics**
  ```bash
  btrfs device stats /mnt/btrfs
  ```