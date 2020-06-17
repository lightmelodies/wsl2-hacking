# wsl2-hacking

# build a new kernel
Download latest kernel from [kernel.org](https://www.kernel.org/)
```console
patch -p1 < ../kernel.patch
zcat /proc/config.gz > .config
make olddefconfig
make -j8
```

# vscode workaround
```console
sed -i 's/WSL_BUILD=0/WSL_BUILD=41984/g' "$(which code)"   
```