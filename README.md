Bash script that dumps PS4 games via FTP connection over the network.
Requires cURL and a PS4 FTP server that supports SELF decryption.
For maximum speed, a gigabit cable connection is recommended.

    Usage: ftpdump [OPTIONS] IP_ADDRESS[:PORT] [OUTPUT_DIRECTORY]
    
    1) Insert a disc and install the game.
    2) Start the FTP server payload on your PS4 (HEN not needed).
    3) Press the PS button (no other button) to leave the browser.
    4) Run the game.
    5) Run this script.

    To dump more games, repeat steps 1), 4), 5).

    Before running the script, make sure the game is completely installed.
    Should the dumping process be interrupted, please delete partial dumps
    before trying again.

    Options:
      -a, --app-only    Dump app only.
      -d, --dlc-only    Dump DLC only.
      -h, --help        Print usage information.
      -p, --patch-only  Dump patch only.
      -s, --sflash      Dump sflash0 and quit.

If no output directory is specified, the current directory will be used.
The dumps will take place in the following subdirectories:

    CUSAXXXXX-app
    CUSAXXXXX-patch

Optionally, IP address and port can be saved inside the script:

    ip=192.168.xxx.xxx
    port=1337

The PC speaker can be used to beep when a dump is complete:

    beep=true
    beep_time=60 (in seconds)

Depending on your computer and operating system, you might not have a PC speaker or must enable it first.

Note that I did not have time to test the script with many different games.
For trouble shooting, you can enable debug messages and/or see cURL's status messages by prefixing the command:

    DEBUG=1 ftpdump ...
    CURL_VERBOSE=1 ftpdump ...
    DEBUG=1 CURL_VERBOSE=1 ftpdump ...

If the script does not run as expected, please report bugs at https://github.com/hippie68/ftpdump/issues.
