KPS -- Kurisutina Packaging System.

Main parts:
 - Phonewave -- system-level package manager 
 - Dmail     -- worldline logging changer
 - Timeleap  -- permanent worldline editor
 ...


Files structure:
```
/fgos/
|-- config.toml
|-- repos.toml
|-- repos.d/
|   \-- <reponame>.toml
|
|-- pkgs/          # Storage for all installed packages
|   \-- <pkg>/
|       |-- persisted/ # Dir for persisted files (ver. independent)
|       \-- <ver>/
|             |-- .Content      # List of all files included in package
|             |-- .Package      # Package information
|             \-- <FILES>
|
|-- tmp/           # Temporary dir for preserving packages archives
|
|
|-- worldlines.d/  # Worldline's bases directory
|   |-- alpha
|   \-- beta
|
|-- dmails/        # History of dmails
|   \-- <worldline>/
|    	|-- 001_20250115T143000_add_coreutils_9.12
|   	\-- 002_20250116T091500_remove_nano
|    
|-- envs/          # Builded work enviroment
|   |-- alpha/
|   \-- beta/
|
|-- kps    # symlink to current rootline's kps-core package directory
|-- rootline # symlink to system root worldline

```

Main rules:
 - initramfs is main root!
 - Every user have ~/.fgos-env with custom enviroment (worldline)


config.toml:
```
Portable=yes   # Totally disables rootline and any system's root editing. For use in differend distros.

```

worldline.toml:
```
[World]
inherit = ""

[Packages]
kernel    = "7.0.9"
libc      = "2.43"
libstdcpp = "*"     # * means latest
dinit = { 
    version = "*", 
    fork = "",         # identifier for forking. For example fork="test" will clone /fgos/pkgs/dinit/5.1 into /fgos/pkgs/dinit/5.1-test and will use it. For editing package.
    persisted = [       # list which files should be persisted
        "/etc/config"   # copy /etc/config from original package into /fgos/pkgs/dinit/persisted/
    ],
    rules = [
                        # rules for linking
    ],
}
gcc       = "*"
coreutils = ""

```
Files in /fgos/pkgs/<pkg>/persisted directory overrides any package's original files.

.../persisted/<structure> mimics original package structrure

If DIR-MD5 != real md5 && fork == null =>
    notice user
    set fork="<random>"
    update all worlds with <pkg>.fork="<random>"


## Package Core

.Content.toml
```toml
Files = [
/bin/vim
/bin/vimtutor
/bin/xxd
]

Links = [

    [ "/bin/ex", "/bin/vim" ],
    [ "/bin/rview", "/bin/vim" ],
    [ "/bin/rvim", "/bin/vim"],
    [ "/bin/vimdiff", "/bin/vim" ]
]

Rules = [
    [ "/bin/", "/sbin/" ] # All files in /bin of packages will be linked into /sbin in system
]

```

.Package
```toml
[PACKAGE]
NAME="vim"
VERSION="9.11"
DESCRIPTION="Highly configurable, keyboard-driven text editor built for extreme speed and efficiency"
MAINTAINER="Hlupa"
BUILD_DATE="21.06.2026"
BUILD_TIME="01:36"

DIR_MD5="338ebe4676e7834cc0d3530e51f2228d"

[DEPENDS]

libc6   >= "2.15"
ncurses =  "*"
```

## Misc

Initramfs contains ONLY initscript which builds entire distro by creating links to packages in mounted /fgos/pkgs/<pkg>.

Boot process:
```
 1. Kernel mounts rootfs
 2. Runs /init -> custom scripts
    Script:
    2.0 Mounts /proc /dev /sys /run
    2.1 Mounts fgos as /fgos/
    2.2 Runs /fgos/kps/bin/phonewave --rootline
        2.2.1 KPS reads /fgos/rootline
        2.2.2 KPS starts generating main / (makes rootline symlinks):
              /fgos/pkgs/<pkg>/persisted -> anywhere in /
              /fgos/pkgs/<pkg>/etc/*         -> /etc/
              /fgos/pkgs/<pkg>/bin/*         -> /usr/bin/
              /fgos/pkgs/<pkg>/sbin/*        -> /usr/sbin/
              /fgos/pkgs/<pkg>/lib/*         -> /usr/lib/
              /fgos/pkgs/<pkg>/lib64/*       -> /usr/lib64
              /fgos/pkgs/<pkg>/usr/lib/*     -> /usr/lib/
              /fgos/pkgs/<pkg>/usr/lib64/*   -> /usr/lib64/
              /fgos/pkgs/<pkg>/usr/libexec/* -> /usr/libexec/
              /fgos/pkgs/<pkg>/usr/include/* -> /usr/include/
              /fgos/pkgs/<pkg>/usr/share/*   -> /usr/share/


    2.3 Mounts /boot /home /var /opt
    2.4 Exec chroot $init-path 2
```

Packages format: .tar.xz (max. compression)
In far far future add support for pacman's .tar.zstd; .deb?




FHS guts:
```
/boot  -- separated partition (mount!)

/fgos  -- separated partition (mount!)

/      -- initramfs
/boot  -- initramfs part

/etc   -- dynamic links (/fgos/pkgs/<pkg>/etc + /fgos/pkgs/persisted/.)
/bin   -- dynamic links
/sbin  -- dynamic links
/home  -- separated partition (mount!)
/var   -- separated partition (mount!)
/opt   -- separated partition (mount!)
/root  -- link to /home/root/

/usr   -- dynamic links
/lib   -- link to /usr/lib
/lib64 -- link to /usr/lib64


/sys   -- mount!
/dev   -- mount!
/proc  -- mount!
/run   -- ...
/tmp   -- ...
/srv   -- ...
/media -- ...
/mnt   -- ...
```


## License

[Fuck your license](./LICENSE)
