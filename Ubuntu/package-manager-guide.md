#### Package Manager Guide

To list all installed package
```sh
$ sudo apt --installed list
```

To list all installed packages you installed
```sh
$ sudo apt --installed list | grep "dotnet"
```

To remove a specific package
```sh
$ sudo apt-get remove --purge PACKAGE_NAME
```

example: 
```sh
$ sudo apt-get remove --purge dotnet-dev-1.0.1
```

To upgrade a specific package
```sh
$ sudo apt-get upgrade PACKAGE_NAME
```
example:
```sh
$ sudo apt-get upgrade dotnet-sdk-2.1
```