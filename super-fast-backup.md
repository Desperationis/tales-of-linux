## Super Fast Backup
The moment I saw this I had to write about it. I usually have to take an entire afternoon to backup a 1TB partition on a 1TB SSD by compressing it in real time using gzip so as to not fill out the entire SSD, but I noticed the reason it was so slow was because it was only using one core. Here is the secret sauce command:

```dd if=/dev/nvme0n1p3 bs=128K  | pv | pigz --fast  > backup.gz```

[pigz](https://github.com/madler/pigz) is a implementation of gzip that uses all the cores in your machine. With the --fast option, it increases the speed of compression by about 4 times. So basically, my 39 hour backup process has turned into less than an hour by pumping all the 32 cores on my laptop to the absolute max. 

After going on stack overflow and using ChatGPT, you can also use these scripts to split the large active smaller files IN PLACE (just don't exit the process while its running, otherwise you lose your data.

```split.bash```

```bash
#!/bin/bash
# (c) whitequark 2010
# (c) dertalai 2015 (minimal modifications)

set -e

if [ $# != 2 ]; then
  echo "Usage: $0  "
  echo "  This script will split file to multiple parts, starting from"
  echo "  the end, and truncating the original file in process."
  echo "  Part size is specified in megabytes (1 MB = 1048576 bytes)."
  echo "  Use at your own risk."
  exit 0
fi

filename=$1
#partsize=$2
partsizeMB=$2
partsize=$(($2 * 1048576))

size=$(stat -c '%s' "${filename}")
parts=$(($size / $partsize))

do_split() {
  _part=$1
  _size=$2

  echo "Splitting part $_part"
  echo $(($partsize * ($_part - 1)))
  dd if="${filename}" of="${filename}.$(printf '%04d' $_part)" \
      count=$partsizeMB bs=1M skip=$((($_part - 1) * $partsizeMB))
  echo "Truncating source file"
  truncate "${filename}" --size="-$_size"
}

lastsize=$(($size % $partsize))
if [ $lastsize != 0 ]; then
  do_split $(($parts + 1)) $lastsize
fi

for i in $(seq $parts -1 1); do
  do_split $i $partsize
done

rm "${filename}"
```


```join.bash```

```
#!/bin/bash
# join.bash
# (c) YourName 2024

set -e

if [ $# != 1 ]; then
  echo "Usage: $0 [PREFIX]"
  echo "  This script will join split parts created by the split script."
  echo "  It concatenates all parts named [PREFIX].0001, [PREFIX].0002, etc."
  echo "  and reconstructs the original file while deleting each part after joining."
  exit 1
fi

prefix=$1

# Find all parts matching the prefix with four-digit suffix
parts=($(ls ${prefix}.[0-9][0-9][0-9][0-9] 2>/dev/null | sort))

if [ ${#parts[@]} -eq 0 ]; then
  echo "No parts found with prefix '${prefix}'."
  exit 1
fi

output="${prefix}"

echo "Joining parts into '${output}'..."

# Initialize or create the output file
> "${output}"

# Iterate over each part, append to output, and delete the part
for part in "${parts[@]}"; do
  echo "Processing '${part}'..."
  cat "${part}" >> "${output}"
  rm "${part}"
  echo "Deleted '${part}'."
done

echo "Join completed successfully."
```
