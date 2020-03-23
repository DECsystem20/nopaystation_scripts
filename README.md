# nopaystation\_scripts

A linux shell script collection which downloads nopaystation PS Vita stuff.
There are five scripts. One to download all \*.tsv files of NoPayStation. The other four are for downloading games, updates or all DLC of a PS Vita game.

Be sure to checkout out the [t7z branch](https://github.com/sigmaboy/nopaystation_scripts/tree/t7z) to create reproducable archives and to download a whole region at once

## Requirements
* a working internet connection
* posix shell (bash, ksh, zsh, sh)
* curl or wget
* [*pkg2zip*](https://github.com/mmozeiko/pkg2zip)
* latest [*mktorrent*](https://github.com/Rudde/mktorrent)

(Optional) To compile+install mktorrent v1.1 (needed for source flag).
Check the version installed on your system.
```bash
$ mktorrent -v
$ git clone https://github.com/Rudde/mktorrent.git
$ cd mktorrent/ && PREFIX=$HOME make
$ PREFIX=$HOME make install
$ rm -rf ~/mktorrent
```
Make sure that executable bit is set on the scripts.

## Installation
```bash
$ git clone https://github.com/sigmaboy/nopaystation_scripts.git && cd nopaystation_scripts
$ chmod +x download*.sh
$ test -d "${HOME}/bin" && ln -s "$(pwd)"/download*.sh "${HOME}/bin"
```


## Script examples

### download\_tsv.sh
It downloads every \*.tsv file from NoPayStation.com and creates a tar archive with the current date for it.
```bash
$ ./download_tsv.sh /path/to/the/output_directory
```
If you don't add the output directory as the first parameter, it uses the current working directory.
You need the \*.tsv file(s) for every other script in this toolset.

### download\_game.sh
With this script you can download a PS Vita game.
The first parameter is the path to your \*.tsv file and the second is the game's title ID.
It places the \*.zip file in the current directory.
For example:
```bash
$ ./download_game.sh /home/tux/Downloads/GAME.tsv PCSE00986
```
I can recommend [this](http://renascene.com/psv/) site for searching title IDs.

### download\_update.sh
With this script you can download the latest or all available PS Vita game updates.
There is a  first parameter optional parameter "-a" and the second is the game's title ID.
It places the files in a created directory from the current working directory named $TITLE\_ID\_update.
For example:
```bash
$ ./download_update.sh [-a] PCSE00986
```

### download\_dlc.sh
This script downloads every DLC found for a specific title ID with available zRIF key.
It places the files in a created directory from the current working directory named $TITLE\ID\_dlc.
For example:
```bash
$ ./download_dlc.sh /home/tux/Downloads/DLC.tsv PCSE00986
```
Every DLC is placed in a created directory named like the title id relative to the current directory.

### download\_psp.sh
With this script you can download a PSP game.
The first parameter is the path to your \*.tsv file and the second is the game's title ID.
It places the \*.iso file in the current directory.
For example:
```bash
$ ./download_psp.sh /home/tux/Downloads/PSP_GAMES.tsv NPUZ00001
```
I can recommend [this](http://renascene.com/psp/) site for searching title IDs.

### download2torrent.sh
Requirements:
* pkg2zip and the latest mktorrent 1.1
  (1.0 is not working since it doesn't know the source option)

This script downloads the game, every update and dlc found for a specific title ID with available zRIF key.
It puts the DLC and the Updates in a dedicated folder named like the generated zip and creates a torrent for the game, updates and dlc folders.
In fact it uses the three scripts from above, combines them to share them easily via BitTorrent. You need to have download\_game.sh, download\_update.sh, download\_dlc.sh in your $PATH variable to get it working.
You must symlink them to **${HOME}/bin/**, **/usr/local/bin** or **/usr/bin/**.
This is explained in the *Installation* Section above

If you want to do some additional steps after running *download2torrent.sh*, you can add a post script named *download2torrent_post.sh* to the directory where you run *download2torrent.sh* from the command line.
It has to be executable to run. *download2torrent.sh* runs the post script with the game name as the first parameter.
Your script can handle the parameter with the variable **$1** in your (shell) script.
You can use this to automate your upload process with an script which adds the torrent to your client or move it and
set the correct permissions to the file.
All files are named like **$1**.
For example the update and dlc directories
* ${1}_update
* ${1}_dlc

or the torrent files
* ${1}.torrent
* ${1}_update.torrent
* ${1}_dlc.torrent

If you call the script with "-a" as the first parameter, it will download all updates instead of the latest only. Additionally you can set the source tag as the end last command line parameter. The <SOURCE> parameter is also optional.
To use this feature you need to have mktorrent installed in version 1.1+!
For example:
```bash
$ ./download2torrent.sh [-a] PCSE00986 http://announce.url /path/to/directory/containing/the/tsv/files <SOURCE>
```
