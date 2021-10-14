Bash script that dumps PS4 games via FTP connection over the network.
Requires cURL and a PS4 FTP server that supports SELF decryption.
For maximum speed, a gigabit cable connection is recommended.

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
      -d, --dlc         Dump DLC data.
          --appdb       Dump app.db file and quit.
          --dump PATH   Dump specified FTP file or directory and quit.
                        Directories must end with a slash: "PATH/".
      -h, --help        Print usage information.
      -k, --keystone    Dump original keystone.
          --no-decrypt  Do not enable SELF decryption.
      -p, --patch       Dump patch data.
      -s, --sflash      Dump sflash0 file and quit.

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

You can enable debug messages and/or see cURL's status messages by prefixing the command:

    DEBUG=1 ftpdump ...
    CURL_VERBOSE=1 ftpdump ...

To compare the dumped directory with a reference dump (e.g. one created by a dumper payload), type:

    diff -r DUMP_DIRECTORY_1 DUMP_DIRECTORY_2

If the script does not run as expected, please report bugs at https://github.com/hippie68/ftpdump/issues.

### For Windows users:
The script runs on Windows 10/11 via WSL (https://docs.microsoft.com/windows/wsl/install). Inside WSL, it is possible to run .exe programs, too, e.g. other command line PKG tools to automate dumping, modifying, and converting to PKG.

### For macOS users:
Having GNU dd instead of the default macOS dd could improve the overall dumping speed slightly:

    brew install coreutils