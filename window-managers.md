## What is a window manager?

Put simply, a window manager organises you windows and decides how GUI elements are placed.\
There are two main types of Window Managers (WMs):
- Stacking Window Managers (SWMs)
- Tiling Window Managers (TWMs)

An SWM is your vanilla window manager. You use your mouse to stack, arrange and rearrange windows. There's normally a system tray, and a button to minimise all apps to it.

A TWM is a bit more advanced tham an SWM. Their main focus is to increase productivity by keeping your hands on the keyboard.\
→ There's keybinds for all functions, however, they have a much steeper learning curve.

## My advice?

Use a stacking window manager if you use the Nano text editor, and use a tiling window manager if you use Vim, Neovim or Emacs.

## Different SWMs

This section will cover Stacking Window Managers.

<details>
  <summary><h4>KDE Plasma</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/plasma.png)

To install KDE Plasma, use the below command:
```
sudo pacman -S plasma konsole dolphin ark kwrite kcalc spectacle krunner partitionmanager
```

Packages         | Description
---------------- | ------------------------------------
plasma           | KDE Plasma Desktop Environment.
konsole          | KDE Terminal.
dolphin          | KDE File Manager.
ark              | Archiving Tool.
kwrite           | Text Editor.
kcalc            | Scientific Calculator.
spectacle        | KDE screenshot capture utility.
krunner          | KDE Quick drop-down desktop search.
partitionmanager | KDE Disk & Partion Manager.
</details>

<details>
  <summary><h4>GNOME</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/gnome.png)

To install GNOME, use the below command:
```
sudo pacman -S gnome gnome-extra
```

```gnome-extra``` is for a more integrated desktop experience, and includes a bundle of apps to facilitate that.
</details>

> Note: Wayland support on LXQt and XFCE is a bit shaky.

<details>
  <summary><h4>LXQt</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/lxqt.png)

To install LXQt, use the below command:
```
sudo pacman -S lxqt
```
</details>

<details>
  <summary><h4>XFCE</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/xfce.png)

To install XFCE, use the below command:
```
sudo pacman -S xfce xfce4-goodies
```

```xfce4-goodies``` is for a more integrated desktop experience, and includes a bundle of apps to facilitate that.

</details>

## Different TWMs

> Images used are meant to demonstrate their customisability and do not reflect what they look like out-of-the-box.

<details>
  <summary><h4>Hyprland</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/hyprland.png)

To install hyprland, use the below command:
```
sudo pacman -S hyprland
```
</details>

> Note: None of the below support Wayland.

<details>
  <summary><h4>Awesome</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/awesome.png)

To install AwesomeWM, use the below command:
```
sudo pacman -S awesome
```

Create a new configuration directory:
```
mkdir ~/.config/awesome
```

And copy the default config file:
```
cp /etc/xdg/awesome/rc.lua ~/.config/awesome/rc.lua
```

</details>

<details>
  <summary><h4>i3WM</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/xfce.png)

To install i3WM, use the below command:
```
sudo pacman -S i3
```

Press enter when prompted for the different packages available.\
Append ```exec i3``` to your ```~/.xinitrc``` file by running the below line:

```
echo 'exec i3' >> ~/.xinitrc
```

</details>

<details>
  <summary><h4>BSPWM</h4></summary>

![](https://raw.githubusercontent.com/Exvix/arch-install-guide/main/images/bspwm.png)

To install BSPWM, use the below command:
```
sudo pacman -S bspwm
```

Append ```exec bspwm``` to your ```~/.xinitrc``` file by running the below line:

```
echo 'exec bspwm' >> ~/.xinitrc
```

</details>
