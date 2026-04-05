First a tar.gz file of artix has to be created to import into wsl2.
If you want to create rootfs.tar.gz by your own using rootfs.img of official Artix-iso, than go on in Windows 10 as follows:
 - Download zip file of newest squashfs-tools-ng versiong at https://infraroot.at/pub/squashfs/windows. Extract archive into a folder.
 - Download artix-s6-YYYYMMDD-x86_64.iso at https://eu-mirror.artixlinux.org/iso/.
 - Mount the iso-file within Windows Explorer.
 - Copy /LiveOS/rootfs.img of mounted iso-file into the /bin subfolder of squashfs-tools-ng directory.
 - Open Windows Command Prompt and change to bin-subfolder in the extracted squashfs-tools-ng directory. 
 - Run in Windows Command Prompt: ```sqfs2tar rootfs.img > artix-rootfs.tar```
 - Compress artix-rootfs.tar to artix-rootfs.tar.gz by 7-zip.  

On Windows 10 open Command Prompt and type:

     $ mkdir %homedrive%%homepath%\wsl\artix
     $ cd %homedrive%%homepath%\wsl\artix
Now copy over the generated artix-rootfs.tar.gz to your start directory (%USERPROFILE%\wsl\artix). 
In Command prompt in said directory now run the following commands to import your artix-rootfs.tar.gz file to create your linux enviroment.

     $ wsl --import artix %homedrive%%homepath%\wsl\artix artix-rootfs.tar.gz
     $ wsl -d artix

An automatic switch to Linux OS follows. 
 
 Go on typing: 
   
     [<PC_NAME> Artix]# pacman-key --init
     [<PC_NAME> Artix]# pacman-key --populate artix
     [<PC_NAME> Artix]# pacman -Syu
     [<PC_NAME> Artix]# pacman -S sudo nano make gcc which autoconf automake pkgconf patch bison flex cmake rpcsvc-proto gperf python intltool re2c diffutils git wget libtool
     [<PC_NAME> Artix]# nano /etc/pacman.conf  

If pacman fails then your chosen Artix release is likley too old.

> [!NOTE]
> At the time of writing this pacman can have an issue with nvidia firmware so if you encounter a conflicting files error run the fix at the bottom of these instructions:


Enable lib32 repository by uncommenting the two lines of the [lib32] section:
 
     [lib32]
     Include = /etc/pacman.d/mirrorlist

Save file and exit nano.

Go on typing: 
					 
     [<PC_NAME> Artix]# pacman -Syy
     [<PC_NAME> Artix]# pacman -S sudo lib32-glibc lib32-gcc-libs lib32-zlib lib32-libelf
     [<PC_NAME> Artix]# nano /etc/wsl.conf  

Append following two lines at end of wsl.conf:
					
      [interop]
      appendWindowsPath=false
					 
Save file and exit nano.

> [!NOTE]  
> \<username> should be replaced with a username of your choice.

Go on typing: 

     [<PC_NAME> Artix]# echo "%wheel ALL=(ALL)  ALL" > /etc/sudoers.d/01wheel
     [<PC_NAME> Artix]# useradd -m -G wheel <username>
     [<PC_NAME> Artix]# passwd <username>
     [<PC_NAME> Artix]# ln -s /usr/lib/libmpc.so /usr/lib/libmpc.so.2
     [<PC_NAME> Artix]# ln -s /usr/lib/libmpfr.so /usr/lib/libmpfr.so.4
     [<PC_NAME> Artix]# exit

Rem.: An automatic switch to Windows 10 OS follows.

Go on typing:
   
     $ wsl --manage artix --set-default-user <username>
     $ wsl -d artix

Rem.: An automatic switch to Linux OS follows.
> [!IMPORTANT]  
> To get rid of perl locale warnings on compilation run the following commands on linux:  
> ```
> sudo sed -i 's/^#\(en_US.UTF-8 UTF-8\)/\1/' /etc/locale.gen
> sudo locale-gen
> ```

Go on typing:
 
     [<username>@<PC-NAME> Artix]$ cd $HOME
     [<username>@<PC-NAME> Artix]$ nano .bashrc  (add the following with a new line at the end of the file:
	  				                cd ~
				                      Save changes with <Ctrl>-X, confirm saving the buffer with "y" and confirm file name with <Return>)
     [<username>@<PC-NAME> ~]$ git config --global core.eol lf
     [<username>@<PC-NAME> ~]$ git config --global core.autocrlf false
     [<username>@<PC_NAME> ~]$ git clone https://github.com/john9527/asuswrt-merlin
     [<username>@<PC_NAME> ~]$ git clone https://github.com/Minecraftboss318/Artix_Asuswrt-Merlin_John_fork Artix_asuswrt	
     [<username>@<PC_NAME> ~]$ cd Artix_asuswrt
     [<username>@<PC_NAME> Artix_asuswrt]$ ./build_asuswrt-mips.sh (or ./build_asuswrt-arm.sh)

The building process will start.

------------------------------------------------------------

Nvidia conflicting firmware fix:

	 [<PC_NAME> Artix]# sudo pacman -Rdd linux-firmware
     [<PC_NAME> Artix]# sudo pacman -Syu linux-firmware
