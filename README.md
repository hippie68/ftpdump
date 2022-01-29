Bash script that dumps PS4 games via FTP connection over the network.
Requires cURL, GNU Wget, and a PS4 FTP server that supports SELF decryption.
For maximum speed, a gigabit cable connection is recommended (but Wi-Fi works, too).

- No USB device required
- No reboots and re-jailbreaking required (keep on dumping!)
- Automatically replaces encrypted trophies
- Can extract PKG and PFS image files
- Supports the updated FTP payload at https://github.com/hippie68/ps4-ftp for best performance
- Runs on Windows 10/11 via WSL, Linux, macOS, and anything that can use Bash

Example command:

    $ ./ftpdump 192.168.178.100

General usage:

    Usage: ftpdump [OPTIONS] HOSTNAME|IP_ADDRESS[:PORT]
    
    1) Insert a disc and install the game. Optional: visit orbispatches.com
       to download and install a game patch compatible with your firmware.
    2) Start a PS4 FTP server (recommended: https://github.com/hippie68/ps4-ftp).
    3) Press the PS button to leave the browser.
    4) Run the game.
    5) Run this script.
    
    To dump more installed games, repeat steps 4) and 5).
    
    Before running the script, make sure the game is completely installed.
    Exit the script at any time by pressing CTRL-C.
    
    Options:
      -a, --app            Dump app data.
          --appdb          Dump app.db file and quit.
          --beep           Beep when done.
      -d, --dlc            Dump DLC data.
          --debug          Print debug information.
          --debug-pfs      Print debug information when extracting a PFS image file.
          --dump PATH      Dump specified FTP file or directory and quit.
                           Directories must end with a slash: "PATH/".
          --dump TITLE_ID [TITLE_ID ...]
                           Dump PKG files that have the specified Title ID(s) and
                           quit. Can be combined with options --app, --patch, --dlc.
          --extract-pfs PFS_IMAGE_FILE
                           Extract a local PFS image file and quit.
          --extract-pkg PKG_FILE
                           Extract a local PS4 PKG file and quit.
          --fix-remaster PARAM_SFO_FILE
                           Apply the "remaster" fix to a local param.sfo file and
                           quit. Requires program "sfo".
      -h, --help           Print usage information.
      -k, --keystone       Dump original keystone.
          --keep-trying    Infinitely keep trying to connect.
          --list[N]        Print a sorted list of installed PKGs and quit. Can be
                           combined with options --app, --patch, --dlc.
                           Optional number N specifies the sort order: 1: Title ID,
                           2: Type, 3: Database status, 4: PKG status, 5: Title.
          --no-decrypt     Do not use server-side SELF decryption.
      -o, --output-dir DIRECTORY
                           Set the root output directory for dumps and extractions.
      -p, --patch          Dump patch data.
      -r, --resume         Resume a previously interrupted download. When used with
                           legacy FTP servers, this can corrupt decrypted files.
      -s, --sflash         Dump sflash0 file and quit.
          --shutdown       Send the custom FTP command "SHUTDOWN" and quit. If the
                           FTP server understands the command, it will stop running.
          --use-pfs        Instead of downloading files separately, download and
                           extract the PFS image file.
      -v, --verbose        Print the client/server dialog when downloading files.

By default, app, patch, and DLC data will be dumped. If no output directory is specified, the current directory will be used.

The dumps will take place in the following subdirectories:

    TITLE_ID-app
    TITLE_ID-patch
    TITLE_ID-dlc
    TITLE_ID-keystone

Optionally, IP address and port can be saved inside the script:

    ip=192.168.xxx.xxx
    port=1337

The PC speaker can be used to beep when a dump is complete:

    beep=true
    beep_time=60 (in seconds)
    beep_interval=3 (in seconds)

Depending on your computer and operating system, you might not have a PC speaker or must enable it first.
By default, the script will only beep when dumping app/patch/DLC data, unless option --beep is specified.

#### List and download PKG files

To list information about installed PKGs, use option --list. The output would look like this:

    Title ID    Type    Location   DB   PKG  Title*
    CUSA07010   App     internal   OK   OK   Sonic Mania
    CUSA07010   DLC     internal   OK   1/1  Sonic Mania
    CUSA07010   Patch   internal   OK   OK   Sonic Mania
    CUSA11993   DLC     internal   OK   -    [App not installed]
    FLTZ00003   App     internal   OK   OK   Remote PKG installer
    LAPY20001   App     internal   OK   OK   PS4-Xplorer
    PSNE00001   App     internal   OK   OK   pSNES - Portable Snes9x

Column "DB" ("Database"):
- "OK" means the PS4 database knows about the Title ID
- "-" means the PS4 database does not know about the Title ID

Column "PKG":
- "OK" means the PKG file exists
- "-" means there is no PKG file and the directory is empty
- "x/y" means x out of y DLC PKG files that had been installed at some point are still there

*The "Title" column is only displayed if the program "sfo" is available.

Use option --listN instead of --list to change the list's default sort order. For example, to sort by title (6th column): "--list6".  
Note: The script employs simultaneous FTP connections to create the list as fast as possible. FTP servers that don't support simultaneous connections, e.g. GoldHEN 2.0's FTP server, can only check 1 file at a time and thus will slow down the process a lot.

PKG files can be downloaded by using option --dump with one or multiple Title IDs, e.g.:

    --dump cusa07010 fltz00003 lapy20001

The files will be stored in the following subdirectories:

    TITLE_ID-app-pkg
    TITLE_ID-patch-pkg
    TITLE_ID-dlc-pkg

Both --list and --dump can be combined with --app, --patch, and --dlc to list and download PKG files of a specific type.

#### Adding the program "sfo"

Optionally, some of the script's functions can use the program "sfo" (https://github.com/hippie68/sfo). To enable them, put a compiled sfo binary file in the script's directory (Windows users see below). Alternatively, edit the script's variable "sfo_path" to point to sfo's location.

### Troubleshooting

You can enable debug messages and/or see cURL's status messages by using options --debug and --verbose.

To compare the dumped directory with a reference dump (e.g. one created by a dumper payload), type (on Unix/WSL):

    diff -r DUMP_DIRECTORY_1 DUMP_DIRECTORY_2

Please note that GoldHEN 2.0's FTP server uses a different decryption method. Which means some .sprx files may differ due to stripped zeros, but they should be fully functional. This also means resuming decrypted files via option --resume will corrupt the files if you resume a partial dump done by a different FTP server with GoldHEN 2.0's FTP server and vice versa.

If the script does not run as expected, please report bugs at https://github.com/hippie68/ftpdump/issues.

### For Windows users:

The script runs on Windows 10/11 via WSL (https://docs.microsoft.com/windows/wsl/install).

After having installed WSL, for convenience you could: 

Download the ZIP file from GitHub: select the green "Code" button, then "Download ZIP". Extract the ZIP file.  
In the same folder that has the file "ftpdump", create a batch file named "ftpdump.bat" that has the following content:

    wsl -e ./ftpdump %*

Then, running the script is as simple as this (replace the IP address with your PS4's IP and FTP port):

    ftpdump 192.168.178.100:1337

Other options can be passed, too, for example:

    ftpdump 192.168.178.100:1337 -p --dlc

To save IP and port permanently, open and edit the file "ftpdump" with a text editor that supports Unix format (Notepad should do).

If Wget is not installed by default, you can install it by opening a Windows command prompt and entering:

    wsl -e sudo apt install wget

#### sfo and WSL's temporary files
In theory, putting sfo.exe or sfo_32.exe into ftpdump's folder should work. However:

*If you use WSL 1:*  
You may run into the trouble that .exe files can't access WSL's temporary files. If so, you need to use a Linux binary instead of an .exe file:

64-bit: https://github.com/hippie68/sfo/releases/download/v1.02/sfo  
32-bit: https://github.com/hippie68/sfo/releases/download/v1.02/sfo_32

Please open an issue if those files don't work for you. As a last resort, you could compile sfo yourself from within WSL:

Start WSL, log in with your WSL user and type:

    cd
    apt install build-essential git libcurl4-openssl-dev
    git clone https://github.com/hippie68/sfo
    cd sfo
    gcc sfo.c -o sfo -s -O3

You can copy files from/to WSL with the Windows File Explorer. Usually, when you enter "\\wsl$\Ubuntu" (replace "Ubuntu", which is WSL's default distro, with the distro you have installed), File Explorer will open WSL's file tree. Your WSL user's directory should be in "/home/your-user-name", and the previously compiled file should be "/home/your-user-name/sfo/sfo".

*If you use WSL 2:*  
Hopefully, .exe files are able to access WSL 2's temporary files. If not, follow the steps for WSL 1 to download or compile a Linux binary.

### For macOS users:
You need to install Wget, you might need to update your Bash version, and having GNU dd (part of coreutils) instead of the default macOS dd could improve the overall dumping speed slightly:

    brew install bash coreutils wget

GNU dd will majorly improve performance when extracting PFS images.

sfo could probably be compiled for macOS, too, but as I don't have a Mac, I can't say for sure how to do it.

### Known limitations

Current PS4 FTP servers, which are based on the same code, have some limitations that affect the script's performance:
- Downloading different SELF files in parallel can corrupt SELF decryption, effectively making downloading in parallel a no-go.
- Cancelling the download of huge files (which the script employs to speed things up) won't stop the server from sending the rest of the file. The result is reduced network throughput (plus in extreme cases a PS4 performance decrease). Currently this can be worked around if the FTP server supports the custom command KILL (which the script will then call).
- When decryption is enabled, servers still report the encrypted file size, which can corrupt resuming.

The updated FTP payload at https://github.com/hippie68/ps4-ftp fixes those issues. Using it is strongly recommended to avoid the network throughput bug and to use option --resume without issues.
