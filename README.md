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

    Usage: ftpdump [OPTIONS] HOSTNAME|IP_ADDRESS[:PORT] [OUTPUT_DIRECTORY]
       Or: ftpdump --extract-pfs|--extract-pkg FILE [OUTPUT_DIRECTORY]
    
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
      -a, --app          Dump app data.
          --appdb        Dump app.db file and quit.
      -d, --dlc          Dump DLC data.
          --debug        Print debug information.
          --debug-pfs    Print debug information while extracting a PFS image file.
          --dump PATH    Dump specified FTP file or directory and quit.
                         Directories must end with a slash: "PATH/".
          --extract-pfs PFS_IMAGE_FILE
                         Extract a local PFS image file and quit.
          --extract-pkg PKG_FILE
                         Extract a local PKG file and quit.
      -h, --help         Print usage information.
      -k, --keystone     Dump original keystone.
          --keep-trying  Infinitely keep trying to connect.
          --no-decrypt   Do not tell the FTP server to enable SELF decryption.
      -p, --patch        Dump patch data.
      -r, --resume       Resume a previously interrupted download. In rare cases and
                         with most FTP servers this can corrupt decrypted files.
      -s, --sflash       Dump sflash0 file and quit.
          --shutdown     Send the SHUTDOWN command and quit. If the FTP server is a
                         payload that understands the command, it will stop running.
          --use-pfs      Instead of downloading files separately, download and
                         extract the PFS image file.
      -v, --verbose      Print the FTP client/server dialog while downloading files.

By default, app, patch, and DLC data will be dumped. If no output directory is specified, the current directory will be used.

The dumps will take place in the following subdirectories:

    CUSAXXXXX-app
    CUSAXXXXX-patch
    CUSAXXXXX-dlc
    CUSAXXXXX-keystone

Optionally, IP address and port can be saved inside the script:

    ip=192.168.xxx.xxx
    port=1337

The PC speaker can be used to beep when a dump is complete:

    beep=true
    beep_time=60 (in seconds)
    beep_interval=3 (in seconds)

Depending on your computer and operating system, you might not have a PC speaker or must enable it first.

### Troubleshooting

You can enable debug messages and/or see cURL's status messages by using options --debug and --verbose.

To compare the dumped directory with a reference dump (e.g. one created by a dumper payload), type:

    diff -r DUMP_DIRECTORY_1 DUMP_DIRECTORY_2

Please note that GoldHEN 2.0's FTP server uses a different decrypting method. Which means some .sprx files may differ due to stripped zeros, but they should be fully functional. This also means resuming decrypted files via option --resume will corrupt the files if you resume a partial dump done by a different FTP server with GoldHEN 2.0's FTP server and vice versa.

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

### For macOS users:
You need to install Wget, and having GNU dd (part of coreutils) instead of the default macOS dd could improve the overall dumping speed slightly:

    brew install coreutils wget

GNU dd will majorly improve performance when extracting PFS images.

### Known limitations

Current PS4 FTP servers, which are based on the same code, have some limitations that affect the script's performance:
- Downloading different SELF files in parallel can corrupt SELF decryption, effectively making downloading in parallel a no-go.
- Cancelling the download of huge files (which the script employs to speed things up) won't stop the server from sending the rest of the file. The result is reduced network throughput (plus in extreme cases a PS4 performance decrease). Currently this can be worked around if the FTP server supports the custom command KILL (which the script will then call).
- When decryption is enabled, servers still report the encrypted file size, which can corrupt resuming.

The updated FTP payload at https://github.com/hippie68/ps4-ftp fixes those issues. Using it is especially recommended if you plan on mass dumping in one session.
