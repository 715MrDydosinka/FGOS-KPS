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
|   \-- <pkg>/<ver>/
|             |-- Content      # List of all files included in package
|             |-- Package      # Package information
|             |-- Depends      # List of dependencies
|             \-- <FILES>
|
|-- tmp/           # Temporary dir for preserving packages archives
|
|-- rootline       # Worldline for system root
|
|-- worldlines.d/  # Worldline's bases directory
|   |-- alpha
|   \-- beta
|
|-- dmails/        # History of dmails
|   \-- <worlidline>/
|    	|-- 001_20250115T143000_add_coreutils_9.12
|   	\-- 002_20250116T091500_remove_nano
|    
|-- envs/          # Builded work enviroment
|   |-- alpha/
|   \-- beta/
|
\
 current   # symlink to current worldlines.d/<active_worldline>
```

Packages format: .tar.xz (max. compression); pacman's .tar.zstd; .deb?

Root packages:
kps
dinit
linux-kernel
glibc
libstdcpp


config.toml:
```
Portable=yes   # Totally disables rootline and any system's root editing


```

