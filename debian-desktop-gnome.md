# Debian desktop installation guide

Installation and configuration of Debian 12 (bookworm) for desktop use. 

The guide was developed on a Dell XPS laptop with Intel 64bit CPU and graphics.

### Before installation

1. Backup your files.

2. Download installation media from [https://www.debian.org/](https://www.debian.org/)[^1].

[^1]: Note that as of Debian 12, non-free firmware is included in the normal Debian installer images, so there's no longer need to get a special version from [https://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/](https://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/).

3. Create a bootable USB using an application such as [Rufus](https://rufus.ie/en/) (Windows) or [Etcher](https://etcher.balena.io/) (cross-platform).

4. Think of a good username and password[^2]. 

[^2]: For single-user desktop/laptop use, it's very convenient to use the same password for root and user; in general, this is not recommended for security reasons, however it makes life a lot easier.

5. Use a live media such as [GParted Live](https://gparted.org/livecd.php) to create partitions. This can be also done during installation, but it's much more convenient to do it with a separate tool, especially when dual-booting with Windows[^3]. 

[^3]: When in doubt, do some reading before partitioning. This can be the most tricky part to figure out. Note that creating partitions will destroy all data on any formatted partition.

### Installation

01. Insert installation media and reboot
02. From the installer boot menu select the "Install" or "Graphical Install" option
03. Choose the language to be used for the installation process
04. Choose location (country) to be used for setting the time zone
05. Choose locale. If no locale is defined for the combination of language and country, choose the preferred one from the given list. A locale is a set of values (stored as [environment variables](https://wiki.debian.org/Locale)) such as the system language, character encoding, date and time formatting, default paper size, currency, and first day of the week.
06. Choose keymap (keyboard layout)
07. Configure the network. If both ethernet and wireless interfaces are detected, choose the one to use during installation.
08. Enter preferred hostname, e.g. the computer's name that identifies the system to the network. If the computer will not be part of a network, just make something up.
09. Enter domain name. For home use, leave this blank, unless setting up a home network.
10. Set the root password. For convenience, use the same password for root and user for single-user use.
11. Set full name. For convenience, use the same as the intended username.
12. Set username
13. Set the user password
14. Partition disks: choose "Manual" to assign mount points
15. Select partitions, and assign mount points. Finally, select "Finish partitioning and write changes to disks". When sure, select "Yes" to confirm writing changes to disks.
16. Wait a few minutes for partitions formatting and installation of the base system
17. Select Debian archive mirror. This is the location of the server that will be distributing Debian packages (see [here](https://www.debian.org/mirror/list) for more info). Choose the "deb.debian.org" option, if offered, when in doubt. Leave the HTTP proxy blank, if not applicable. 
18. Select "Yes" to participate in the package usage survey, i.e. to send anonymous statistics about the most used packages
19. Choose software to install:
  - "Debian desktop environment"
  - "GNOME" (and/or any other desktop environment of choice)
  - "standard system utilities"
20. Wait a few minutes for the installation
21. Select "Yes" to install the GRUB boot loader to the primary drive. This will normally let you choose an operating system to boot every time the computer boots. This is closely related to the partitions, so if you don't know what this means, do some reading first.
22. Choose the device for boot loader installation, normally /dev/sda
23. Once installation is complete, remove the installation USB, and hit "Continue" to reboot. Hopefully you will be greeted with the GRUB menu and will be able to choose Debian.

### Configure sudo

Open a terminal and run:

```
$ su
# apt-get install sudo
# /usr/sbin/visudo
```

Look down in the file and find the line:

```
root    ALL=(ALL) ALL
```

Type a new line with YOUR username in the beginning. It should look like this:

```
root    ALL=(ALL:ALL) ALL
user    ALL=(ALL:ALL) ALL
```

Hit CTRL+O and ENTER to save, CTRL+X to close, and CTRL+D to logout.

### Software sources

Open the software sources file:

```
$ sudo nano /etc/apt/sources.list
```

Change the content to:

```
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware

deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware

# bookworm-updates, to get updates before a point release is made;
# see https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_updates_and_backports
deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware

deb http://deb.debian.org/debian bookworm-proposed-updates main contrib non-free
deb-src http://deb.debian.org/debian bookworm-proposed-updates main contrib non-free

deb http://deb.debian.org/debian bookworm-backports main contrib non-free
deb-src http://deb.debian.org/debian bookworm-backports main contrib non-free

# Debian multimedia
deb https://www.deb-multimedia.org bookworm main non-free
```

Add the Debian multimedia key:

```
$ sudo apt-get update -oAcquire::AllowInsecureRepositories=true && sudo apt-get -f install && sudo apt-get install deb-multimedia-keyring
```

Update repos:

```
$ sudo apt-get update && sudo apt-get -y dist-upgrade
```

### Enable/disable Wayland/Xorg

To change from Wayland to XServer (optional):

```
$ sudo nano /etc/gdm3/daemon.conf
```

Uncomment the following line and reboot:

```
#WaylandEnable=false
```

### Enable bash completion

```
$ sudo nano /etc/bash.bashrc
```

Uncomment the section under:

```
# enable bash completion in interactive shells
```

### Customise GRUB menu

```
$ sudo nano /etc/default/grub
```

- Change `GRUB_TIMEOUT=5` to `10`
- Change `GRUB_CMDLINE_LINUX_DEFAULT="quiet"` to `"quiet splash"`
- Uncomment the line `GRUB_GFXMODE=640X480`
- (Optional) To hide GRUB menu, enable the line `GRUB_TIMEOUT_STYLE=hidden`

Update by running `$ sudo update-grub` in the terminal.

### Window buttons

Launch GNOME Tweaks and change:

- Window Titlebars > Maximize: on, Minimize: on

### GNOME Extensions

Launch Firefox and navigate to [https://extensions.gnome.org/](https://extensions.gnome.org/). Install the following extensions:

- Dash to panel: this extension moves the dash into the main panel, combining the application launchers and system tray into a single panel, so that the dock is no longer needed.
- ArcMenu: this extension provides an application menu, with a familiar user experience and workflow. 
- AppIndicator and KStatusNotifierItem Support: this extension allows to show indicator icons in the panel, and reveals indicator menus upon click.

Run the GNOME extensions app with `$ gnome-extensions-app` and configure:

- Launch new instance: on
- Removable Drive Menu: on
- Places Status Indicator: on

### Useful packages

#### Firmware:

```
$ sudo apt-get install -y firmware-linux firmware-iwlwifi firmware-atheros firmware-realtek
```

#### Command-line tools:

```
$ sudo apt-get install -y debian-goodies laptop-mode-tools gdebi-core mc rsync plocate command-not-found tree exiv2 dos2unix unrar ed mtools ntpsec samba-common flatpak img2pdf openssh-server
```

#### Geany text editor:

```
$ sudo apt-get install -y geany geany-plugins
```

#### Spelling tools:

todo
$ sudo apt-get install -y hunspell hunspell-en-gb hunspell-el hunspell-de-de hunspell-de-at

#### Audio and video applications:

```
$ sudo apt-get install -y vlc soundconverter handbrake-gtk deadbeef audacity asunder smplayer easytag gnome-subtitles avidemux sound-juicer pavucontrol lame flac ffmpeg cdparanoia sox shntool cuetools libdvdcss2 libmac10
```

### GNOME enhancements:

```
$ sudo apt-get install -y gnome-system-tools gnome-software-plugin-flatpak gnome-software-plugin-snap gnome-power-manager gnome-dictionary viagee nautilus-dropbox nautilus-admin nautilus-image-converter dconf-editor alacarte gscan2pdf galculator guvcview gparted glabels gnumeric gnucash gnucash-docs
```

#### Misc desktop applications:

```
$ sudo apt-get install -y gimp gimp-data-extras gimp-plugin-registry gimp-texturize gimp-gutenprint inkscape menulibre gprename deluge hugin hardinfo osmo zim zim-tools system-config-printer meld qt5ct grub-customizer mupdf atril
```

#### Scientific applications:

```
$ sudo apt-get install -y git gitg octave octave-statistics maxima wxmaxima gnuplot sqlite3
```

#### LaTeX:

```
$ sudo apt-get install -y texlive texlive-bibtex-extra texlive-science texlive-xetex texlive-fonts-extra texlive-publishers texlive-luatex texlive-lang-german texlive-lang-greek texmaker texstudio dvipng
```

### Install Papirus icon theme

```
$ sudo sh -c "echo 'deb http://ppa.launchpad.net/papirus/papirus/ubuntu focal main' > /etc/apt/sources.list.d/papirus-ppa.list"
$ sudo apt-get install dirmngr
$ sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com E58A9D36647CAE7F
$ sudo apt-get update
$ sudo apt-get install papirus-icon-theme libreoffice-style-papirus
```

Enable the icon theme in Tweaks > Appearance > Icons > Papirus

In case you encounter a keyring issue, and get the warning 

`W: http://ppa.launchpad.net/papirus/papirus/ubuntu/dists/focal/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.`

follow the instructions from [https://itsfoss.com/key-is-stored-in-legacy-trusted-gpg/]. The command will look like this (the key might differ):

```
sudo apt-key export 647CAE7F | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/Papirus.gpg```

### Cleanup

```
$ sudo apt-get autoremove --purge
```

### Update db and font cache:

```
$ sudo updatedb && sudo update-command-not-found && sudo fc-cache -f -v
```

### Configure Geany

- Edit > Preferences > Interface > Toolbar: new, open, save | print | undo, redo | cut, copy, paste | find, find and replace
- View > Show Sidebar: off
- View > Show Message Window: off
- Document > Line Wrapping: on

### Configure language input

- Setting > Keyboard > Input Sources > hit the + sign to add languages
- Settings > Keyboard > Input Source Switching > Switch different sources individually for each window
- Tweaks > Keyboard & Mouse > Additional Layout Options > Switching to another layout > Alt+Shift
- Tweaks > Keyboard & Mouse > Show Extended Input Sources: on
- Tweaks > Keyboard & Mouse > Middle Click Paste: off

### Misc tweaks

- Settings > Power > Show Battery Percentage: on

### Dropbox

Download Dropbox and run the Dropbox daemon, with:

$ cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
$ ~/.dropbox-dist/dropboxd

If the indicator is not working, e.g. no menu appears on right click, lock the Desktop with Super+L and log back in.

### GTK+ look for Qt applications

Qt5 Settings > Appearance > 
  Style: Adwaita
  Standard dialogs: GTK3

### GNOME configurations

- Setting > Multitasking > Workspaces > Fixed number of workspaces > Number of Workspaces = 1

- Settings > Multitasking > General >
  Hot Corner: off
  Active Screen Edges: on

- To toggle F1...F12 keys use media functions or "F" keys, press Fn+Esc

- Run dconf-editor

org > gnome > nautilus > 
  preferences > 
    always-use-location-entry: true
    open-folder-on-dnd-hover: false (disable auto-opening of a folder when using drag and drop in Nautilus)
    show-create-link: true
    search-view: 'icon-view'
    default-sort-order: 'type'
  icon-view
    captions: ['size','type','none']
  gtk > settings > file-chooser > 
    sort-directories-first: true

OR 

$ gsettings set org.gnome.nautilus.preferences open-folder-on-dnd-hover false


### Paper Clip PDF metadata editor

```
sudo apt-get install apt install flatpak gnome-software-plugin-flatpak && sudo flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo && sudo apt-get update && sudo flatpak install flathub io.github.diegoivan.pdf_metadata_editor
```

### Surfshark VPN

The following commands will get the installation script, show script's content, and install Surfshark.

```
curl -f https://downloads.surfshark.com/linux/debian-install.sh --output surfshark-install.sh && cat surfshark-install.sh && sh surfshark-install.sh
```

### Miniconda

Get Miniconda from [here](https://docs.conda.io/projects/miniconda/en/latest/). Miniconda defaults to installing in the ~/miniconda3 directory, but it's better to install it in a hidden directory ~/.miniconda3 instead.

```
mkdir -p ~/.miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/.miniconda3/miniconda.sh
bash ~/miniconda3/.miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/.miniconda3/miniconda.sh
```

### Oracle VirtualBox

Add to the software sources file:

deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] https://download.virtualbox.org/virtualbox/debian bookworm contrib

Download the Oracle public key for verifying the signature and add the key with

wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg

```
sudo apt-get update
sudo apt-get install virtualbox-7.0
```
