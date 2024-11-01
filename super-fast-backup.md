## Super Fast Backup
The moment I saw this I had to write about it. I usually have to take an entire afternoon to backup a 1TB partition on a 1TB SSD by compressing it in real time using gzip so as to not fill out the entire SSD, but I noticed the reason it was so slow was because it was only using one core. Here is the secret sauce command:

```dd if=/dev/sdX of=/dev/null bs=64K | pigz --fast > compressed_file.gz```

[pigz](https://github.com/madler/pigz) is a implementation of gzip that uses all the cores in your machine. With the --fast option, it increases the speed of compression by about 4 times. So basically, my 39 hour backup process has turned into less than an hour by pumping all the 32 cores on my laptop to the absolute max. 
