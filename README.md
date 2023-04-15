Bash script that dumps PS4 games via FTP connection over the network.
Requires cURL, GNU Wget, and a PS4 FTP server that supports SELF decryption.
For maximum speed, a gigabit cable connection is recommended (but Wi-Fi works, too).

- Automatically replaces encrypted trophies.
- Automatically fixes remaster dumps (if the program "sfo" is available).
- Can list and download installed PKG files.
- Can extract PKG and unencrypted PFS image files.
- Supports the improved FTP payload at https://github.com/hippie68/ps4-ftp for best performance.
- Runs on Windows 7/8 or 32-bit Windows 10 via Cygwin, 64-bit Windows 10/11 via WSL, Linux, macOS, and in theory anything that can run Bash.

# Index
- [Manual](#manual)
  - [How to dump](#how-to-dump)
  - [List and download PKG files](#list-and-download-pkg-files)
  - [Adding the program "sfo"](#adding-the-program-sfo)
  - [Troubleshooting](#troubleshooting)
- [Additional information](#additional-information)
  - [Windows](#windows)
  - [macOS](#macos)
  - [Known limitations](#known-limitations)

# Manual

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
          --download PATH  Download specified FTP file or directory and quit.
                           Directories must end with a slash: "PATH/".
          --download TITLE_ID [TITLE_ID ...]
                           Download PKG files that have the specified Title ID(s) and
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
                           combined with options --app, --patch, --dlc. The optional
                           number N specifies the sort order: 1: Title ID, 2: Type,
                           3: Location, 4: Database status, 5: PKG status, 6: Title.
          --no-decrypt     Do not use server-side SELF decryption.
      -o, --output-dir DIRECTORY
                           Set the root output directory for dumps and extractions.
      -p, --patch          Dump patch data.
      -r, --resume         Resume a previously interrupted download. When used with
                           legacy FTP servers, this can corrupt decrypted files.
      -s, --sflash         Dump sflash0 file and quit.
          --shutdown       Send the custom FTP command "SHUTDOWN" and quit. If the
                           FTP server understands the command, it will stop running.
          --use-pfs        While dumping, instead of downloading files separately,
                           download and extract the PFS image file.
      -v, --verbose        Print the client/server dialog when downloading files.

## How to dump

Example command:

    $ ./ftpdump 192.168.1.100

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

## List and download PKG files

To list information about installed PKGs, use option --list. The output would look like this:

    Title ID    Type    Location   DB   PKG  Title\*
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

\*The "Title" column is only displayed if the program "sfo" is available.

Use option --listN instead of --list to change the list's default sort order. For example, to sort by title (6th column): "--list6".
Note: The script employs simultaneous FTP connections to create the list as fast as possible. FTP servers that don't support simultaneous connections, e.g. the FTP server of GoldHEN 2.3 or lower, can only check 1 file at a time and thus will slow down the process a lot.

PKG files can be downloaded by using option --download with one or multiple Title IDs, e.g.:

    --download cusa07010 fltz00003 lapy20001

The files will be stored in the following subdirectories:

    TITLE_ID-app-pkg
    TITLE_ID-patch-pkg
    TITLE_ID-dlc-pkg

Both --list and --download can be combined with --app, --patch, and --dlc to list and download PKG files of a specific type.

## Adding the program "sfo"

Optionally, some of the script's functions can use sfo (https://github.com/hippie68/sfo). To enable it, put a compiled sfo binary file in the script's directory or edit the script variable "sfo_path".

- For Cygwin 32-bit: https://github.com/hippie68/sfo/releases/download/v1.02/sfo_32.exe
- For Cygwin 64-bit: https://github.com/hippie68/sfo/releases/download/v1.02/sfo.exe
- For WSL or 64-bit Linux: https://github.com/hippie68/sfo/releases/download/v1.02/sfo
- For 32-bit Linux: https://github.com/hippie68/sfo/releases/download/v1.02/sfo_32

## Troubleshooting

You can enable debug messages and/or see cURL's status messages by using options --debug and --verbose.

To compare the dumped directory with a reference dump (e.g. one created by a dumper payload), type (on Linux/WSL):

    diff -r DUMP_DIRECTORY_1 DUMP_DIRECTORY_2

Please note that GoldHEN's FTP server uses a different decryption method. Which means some .sprx files may differ due to stripped zeros, but they should be fully functional. This also means resuming decrypted files via option --resume will corrupt the files if you resume a partial dump done by a different FTP server with GoldHEN's FTP server and vice versa.

If the script does not run as expected, please report bugs at https://github.com/hippie68/ftpdump/issues.

# Additional information

## Windows

The script runs on Windows 7/8 and 32-bit Windows 10 via Cygwin (https://www.cygwin.com) and on 64-bit Windows 10/11 via WSL (https://docs.microsoft.com/windows/wsl/install).

After having installed WSL, for convenience you could:

Download the ZIP file from GitHub: select the green "Code" button, then "Download ZIP". Extract the ZIP file.
In the same folder that has the file "ftpdump", create a batch file named "ftpdump.bat" that has the following content:

    wsl -e ./ftpdump %*

Then, the script can be run by opening a Windows command prompt (cmd.exe) and typing (replace the IP address with your PS4's IP and FTP port):

    ftpdump 192.168.1.100:1337

Other options can be passed, too, for example:

    ftpdump 192.168.1.100:1337 -p --dlc

To save IP and port permanently, open and edit the file "ftpdump" with a text editor that supports Unix format. On current Windows 10/11 builds, Notepad should do.

If Wget is not installed by default, you can install it by opening a Windows command prompt and entering:

    wsl -e sudo apt install wget

If you want to create a shortcut that runs predefined ftpdump commands on double-click, save a copy of ftpdump.bat under a different .bat file name, replace "%\*" with desired options and add the line "pause" at the end of the file.

## macOS
You need to install Wget and update your Bash version, and having GNU dd (part of coreutils) instead of the default macOS dd could improve the overall dumping speed slightly:

    brew install bash coreutils grep wget

GNU dd will majorly improve performance when extracting PFS images.
After installing Homebrew's Bash, make sure to adjust the shebang (the script's first line) to point to the correct path.

## Known limitations

Current PS4 FTP servers, which are based on the same code, have some limitations that affect the script's performance:
- Downloading different SELF files in parallel can corrupt SELF decryption, effectively making downloading in parallel a no-go.
- Cancelling the download of huge files (which the script employs to speed things up) won't stop the server from sending the rest of the file. The result is reduced network throughput (plus in extreme cases a PS4 performance decrease). Currently this can be worked around if the FTP server supports the custom command KILL (which the script will then call).
- When decryption is enabled, servers still report the encrypted file size, which can corrupt resuming.
- Files larger than 4 GiB may not resume properly.

The updated FTP payload at https://github.com/hippie68/ps4-ftp fixes those issues. Using it is strongly recommended to avoid the network throughput bug and to use option --resume without issues.
