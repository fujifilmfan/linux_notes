LFS201 Essentials of System Administration Notes
=====

Linux filesystem and paths
=====

Linux paths
-----
  
Linux commands
-----
`$ sudo du -shxc --exclude=proc *`  
`$ du -shc share/*`  
`$ file *cgi-bin/*` # in `/usr/lib/cups` (should be in lib64)  
`$ ls -lF` # difference from `lf -al` ? '-F' appends a symbol at the end of the name corresponding to the type  
`$ ulimit -a` displays or resets resource limits associated with processes running under a shell  
`$ ulimit [options] [limit]` as in `$ ulimit -n 1600` (changes maximum number of file descriptors)  
? What's a file descriptor? (same as file streams? stdin, stdout, stderr)  
  