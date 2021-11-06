Bash script that dumps PS4 games via FTP connection over the network.
Requires cURL and a PS4 FTP server that supports SELF decryption.
For maximum speed, a gigabit cable connection is recommended.

Example command:

    $ ./ftpdump 192.168.178.100

General usage:

    Usage: ftpdump [OPTIONS] HOSTNAME|IP_ADDRESS[:PORT] [OUTPUT_DIRECTORY]
    
    1) Insert a disc and install the game. Optional: visit orbispatches.com
       to download and install a game patch compatible with your firmware.
    2) Start the FTP server payload on your PS4.
    3) Press the PS button (no other button) to leave the browser.
    4) Run the game.
    5) Run this script.
    
    To dump more games, repeat steps 1), 4), 5).
    
    Before running the script, make sure the game is completely installed.
    Should the dumping process be interrupted, please delete partial dumps
    before trying again.
    
    Options:
      -a, --app         Dump app data.
          --appdb       Dump app.db file and quit.
      -d, --dlc         Dump DLC data.
          --debug       Print debug information while dumping.
          --dump PATH   Dump specified FTP file or directory and quit.
                        Directories must end with a slash: "PATH/".
      -h, --help        Print usage information.
      -k, --keystone    Dump original keystone.
          --no-decrypt  Do not try to enable SELF decryption.
      -p, --patch       Dump patch data.
      -s, --sflash      Dump sflash0 file and quit.
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
