# Arch Linux Hyprland Setup



## 1. Arch Linux Install

Install Arch Linux using Arch Linux booted USB and running `archinstall` command.

* During install don't choose any desktop profile.
* Add a user and add it to sudo group
* For bootloader, just use default systemd-boot
* For disk format use `btrfs`
* For audio, choose `pipewire`
* For network configuration, select `Copy ISO network configuration to installation`
* For extra packages, just install `curl` and `git`

Once arch linux installed, reboot and login  as the user created



## 2. Install Yay

We need to install yay before installing Hyprland

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```



## 3. Install Neovim (Optional)

Before installing Hyprland, we need to install Neovim so that we can open and change Hyprland configurations

```bash
yay -S neovim-git
```



## 4. Install Hyprland Dependencies

Before installing Hyprland, we need to install some dependencies that Hyprland needs

You can check if there is any update in dependencies needed by going to the link below:
<https://wiki.hypr.land/Getting-Started/Installation/>

Now, Let's install Hyprland dependencies
```bash
yay -S ninja gcc cmake meson libxcb xcb-proto xcb-util xcb-util-keysyms libxfixes libx11 libxcomposite libxrender libxcursor pixman wayland-protocols cairo pango libxkbcommon xcb-util-wm xorg-xwayland libinput libliftoff libdisplay-info cpio tomlplusplus hyprlang-git hyprcursor-git hyprwayland-scanner-git hyprwire-git xcb-util-errors hyprutils-git glaze hyprgraphics-git aquamarine-git re2 hyprland-qtutils-git muparser
```

Once the dependencies are installed, we can finally install Hyprland

## 5. Install Hyprland

Now Let's install Hyprland by running
```bash
yay -S hyprland-git
```



## 6. Updates needed for Nvidia

Reference: <https://wiki.hypr.land/Nvidia/>

For system with Nvidia graphics card, we need to do few additional configurations.

First we need to install the nvidia driver

```bash
sudo pacman -S nvidia-dkms nvidia-utils nvidia-settings egl-wayland
```

Next we need to rebuild DKMS modules. For that run:
```bash
sudo pacman -S linux-headers
sudo dkms autoinstall
```

To verify the above commands worked, run:

```bash
dkms status
```

You should see something like:
`nvidia/570.xx, kernel-version, x86_64: installed`

Now we need to rebuild initramfs. Run:
```bash
sudo mkinitcpio -P
```

Then, reboot
```bash
reboot
```

Now re-login and now we need to add few configurations

First we need to enable modeset.

To enable it, create and edit `/etc/modprobe.d/nvidia.conf`, and add this line to the file:

```ini
# /etc/modprobe.d/nvidia.conf
options nvidia_drm modeset=1
```

Early KMS will allow the Nvidia modules to load earlier into the boot sequence. On distros using `mkinitcpio`, like Arch, you can enable it by editing `/etc/mkinitcpio.conf`. In the `MODULES` array add the following module names:

```ini
# /etc/mkinitcpio.conf
MODULES=(... nvidia nvidia_modeset nvidia_uvm nvidia_drm ...)
```

Now we need to rebuild initramfs again. Run:

```bash
sudo mkinitcpio -P
```

Then, reboot

```bash
reboot
```



## 6. Install Kitty

Kitty is the terminal emulator which is by default in Hyprland config

```bash
yay -S kitty
```



## 7. Install SDDM

To run Hyprland session on boot, we need to install SDDM display manager.

To install SDDM, run:
```bash
sudo pacman -S sddm
```

Once installed, enable the sddm service, so that it runs automatically on boot
To enable it, run:

```bash
sudo systemctl enable sddm
```



## 8. Reboot

Once Hyprland, Kitty and SDDM are installed, we can reboot the system by running
```bash
reboot
```

Once this is done we have an `Arch Linux + SDDM + Hyprland` setup running. Next is installing other required apps and changing cofigurations

---



## 9. Hyprland Post Setup

Once rebooted, the sddm display manager window appears for login.

At the top left corner, make sure `Hyprland` is chosen. Then type the user password and login

Below are the few necessary default keymaps in Hyprland
| Shortcut    | Action              |
| ----------- | ------------------- |
| `SUPER + Q` | Open the terminal   |
| `SUPER + C` | Close active window |



Once entered the Hyprland session, you are greeted with Hyprland Welcome page in which you press next, it will show that applications for few required and optional functionalities are missing.

1. Authentication agent*
2.  Wallpaper
3. Notification daemon* 
4. Application launcher
5. File manager*
6. XDG Desktop Portal* 
7. Status bar / shell
8. Clipboard*



To install the above missing components, install the following packages

```bash
sudo pacman -S \
polkit-gnome \
swaybg \
mako \
rofi-wayland \
thunar \
xdg-desktop-portal-hyprland \
waybar \
wl-clipboard
```



Enable the NetworkManager service

```bash
sudo systemctl enable NetworkManager
```





**What each one provides**

| Component            | Package                         | Purpose                                 |
| -------------------- | ------------------------------- | --------------------------------------- |
| Authentication agent | **polkit-gnome**                | Allows apps to request admin privileges |
| Wallpaper            | **swaybg**                      | Sets background images                  |
| Notification daemon  | **mako**                        | Shows system notifications              |
| Application launcher | **rofi** (rofi-wayland)         | App launcher                            |
| File manager         | **Nautilus**                    | File browser                            |
| XDG portal           | **xdg-desktop-portal-hyprland** | Required for screen share, Flatpak, etc |
| Status bar           | **Waybar**                      | Top bar                                 |
| Clipboard            | **wl-clipboard**                | Wayland clipboard tools                 |

We need to start some of them automatically on Hyprland session start. To do that in
`~/.config/hypr/hyprland.conf` add:

```ini
exec-once = waybar
exec-once = mako
exec-once = nm-applet
exec-once = swaybg -i ~/wallpaper.jpg -m fill
exec-once = /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1
```



**Optional Packages**

| Component               | Package                 | Purpose                                       |
| ----------------------- | ----------------------- | --------------------------------------------- |
| Network tray applet     | network-manager-applet  | GUI tray for managing NetworkManager          |
| Terminal file manager   | impala                  | Terminal-based file navigation tool           |
| Screenshot tools        | grim slurp              | Wayland screenshot utilities (select region)  |
| Brightness control      | brightnessctl           | CLI utility to control screen brightness      |
| Audio control panel     | pavucontrol             | GUI for managing PulseAudio volumes/devices   |
| Developer font          | ttf-firacode-nerd       | FiraCode Nerd Font with programming ligatures |
| Developer font          | ttf-jetbrains-mono-nerd | JetBrains Mono Nerd Font for coding           |
| Fuzzy finder            | fzf                     | Interactive fuzzy search for files/commands   |
| System monitor          | btop                    | Resource monitor for CPU, memory, disks       |
| Smart directory jumper  | zoxide                  | Faster `cd` replacement with learning paths   |
| Fast search tool        | ripgrep                 | Extremely fast recursive text search          |
| Modern find replacement | fd                      | Simple and fast alternative to `find`         |



### All Packages List

The full list of packages used in this setup can be found here:

[Package List](packages.md)

