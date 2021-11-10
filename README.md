Bash script that dumps PS4 games via FTP connection over the network.
Requires cURL and a PS4 FTP server that supports SELF decryption.
For maximum speed, a gigabit cable connection is recommended.

Example command:

    $ ./ftpdump 192.168.178.100

General usage:

    Usage: ftpdump [OPTIONS] HOSTNAME|IP_ADDRESS[:PORT] [OUTPUT_DIRECTORY]
       Or: ftpdump --extract-pfs|--extract-pkg FILE
    
    1) Insert a disc and install the game. Optional: visit orbispatches.com
       to download and install a game patch compatible with your firmware.
    2) Start an FTP server. Recommended: https://github.com/hippie68/ps4-ftp.
    3) Press the PS button to leave the browser while keeping the server alive.
    4) Run the game.
    5) Run this script.
    
    To dump more installed games, repeat steps 4) and 5).
    
    Before running the script, make sure the game is completely installed.
    Should the dumping process get interrupted, please delete partial dumps
    before trying again.
    
    Exit the script at any time by pressing CTRL-C.
    
    Options:
      -a, --app         Dump app data.
          --appdb       Dump app.db file and quit.
      -d, --dlc         Dump DLC data.
          --download-limit NUMBER
                        Maximum number of simultaneous downloads. Values greater
                        than 1 mean downloads run asynchronously. Only use this if
                        the FTP server can handle simultaneous SELF decryptions.
          --debug       Print debug information.
          --debug-pfs   Print debug information while extracting a PFS image file.
          --dump PATH   Dump specified FTP file or directory and quit.
                        Directories must end with a slash: "PATH/".
          --extract-pfs PFS_IMAGE_FILE
                        Extract a local PFS image file and quit.
          --extract-pkg PKG_FILE
                        Extract a local PKG file and quit.
      -h, --help        Print usage information.
      -k, --keystone    Dump original keystone.
          --no-decrypt  Do not tell the FTP server to enable SELF decryption.
          --no-warning  Do not display general usage warnings. Critical script
                        execution warnings will still be displayed.
      -p, --patch       Dump patch data.
      -s, --sflash      Dump sflash0 file and quit.
          --use-pfs     Instead of downloading files separately, download and
                        extract the PFS image file. It will take longer, but might
                        be useful to prevent PS4 FTP servers from crashing, which
                        eventually will happen when downloading thousands of files.
      -v, --verbose     Increase cURL's verbosity to see the client/server dialog.

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

To save IP and port permanently, open and edit the file "ftpdump" with a text editor that supports Unix format (Notepad should do). Alternatively, you could save them in "ftpdump.bat":

    wsl -e ./ftpdump 192.168.178.100:1337 %*

### For macOS users:
Having GNU dd instead of the default macOS dd could improve the overall dumping speed slightly:

    brew install coreutils

It will majorly improve performance when extracting PFS images.

### Known limitations

Current PS4 FTP servers, which are based on the same code, have some limitations that hinder performance:
- Downloading thousands of files can crash the server. This can be worked around by enabling PFS image extraction via option --use-pfs, which is slower but should work for games that have too many files (speaking of about 15.000+ files).
- Downloading different SELF files in parallel can corrupt SELF decryption, effectively making downloading in parallel a no-go.
- Cancelling the download of huge files (which the script employs to speed things up) won't stop the server from sending the rest of the file. The result is reduced network throughput (plus in extreme cases a PS4 performance decrease). Currently this can be worked around if the FTP server supports the custom command KILL (which the script will then call).

The updated FTP payload at https://github.com/hippie68/ps4-ftp fixes the last two issues.

If the script doesn't work as expected, please report bugs at https://github.com/hippie68/ftpdump.
