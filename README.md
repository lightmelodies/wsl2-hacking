## Kernel

Compile kernel as follow (assuming you are using latest mainline kernel from [kernel.org](https://www.kernel.org/))
```console
patch -p1 < ../kernel.patch
zcat /proc/config.gz > .config
make olddefconfig
make -j8
```

It seems swap does not work, disable it in .wslconfig
```ini
[wsl2]
kernel = /path/to/kernel
swap = 0
```

Workaround for Visual Studio Code
```console
sed -i 's/WSL_BUILD=0/WSL_BUILD=41984/g' "$(which code)"   
```

## Proxy

Just add windows host ip to /etc/hosts.

.zshrc
```console
sudo sed -i '/^\S* windows-host$/d' /etc/hosts 
echo "$(ip r | awk 'NR==1{print $3}') windows-host" | sudo tee -a /etc/hosts > /dev/null
```

.gitconfig
```ini
[http "https://github.com"]
proxy = socks5h://windows-host:7891 
```

.ssh/config
```console
Host github.com
User git
ProxyCommand nc -X 5 -x windows-host:7891 %h %p
```

## Mount
/etc/wsl.conf
```ini
[automount]
enabled = true
root = /
options = "metadata,umask=22,fmask=011"
```
/etc/fstab
```
# <file system> <dir> <type> <options> <dump> <pass>
tmpfs   /tmp    tmpfs rw,nodev,nosuid 0 0
```

## Vim
```console
"clipboard
let s:clip = '/c/Windows/System32/clip.exe' 
if executable(s:clip)
    augroup WSLYank
        autocmd!
        autocmd TextYankPost * call system('echo '.shellescape(join(v:event.regcontents, "\<CR>")).' | '.s:clip)
    augroup END
end

map <silent> =p :r !powershell.exe -Command Get-Clipboard<CR>
map! <silent> <C-r>= :r !powershell.exe -Command Get-Clipboard<CR>
```
