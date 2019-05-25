# grub-theme-creator
Grub-theme-creator (GTC) is a project that automates the creation of a grub2 theme, using a bash script and settings files and resources (images).
It is part of the "PrettyGrub" Project, that's why you'll find this word in several places during usage.

## Disclaimer
This project is developed and tested on my personal computer using Manjaro Plasma Testing. Since it is using common Linux utilities, it should work on other Linux systems as well, so users reports on which systems they used it (successfully or not) is welcome.
As every program, it is possible to have bugs and certainly the code can be improved. Use it on your own responsibility, taking the same risk as when you use other programs. I would appreciate reporting bugs, proposed improvements and feature requests.

## PURPOSE
While experienced users can easily modify a common grub theme to their liking, artistic themes need some talent. Similarly, artists are not always fluent on properly editing grub theme files.
*Grub Theme Creator* is trying to fill those gaps, letting talented artists and less talented (artistically) users create easily a theme in seconds, previewing the result and modifying to get the best result to their expectations.
I would say, this is my appreciation gift to artists!

## USAGE
You can use GTC right away, even with no custom settings files, or no parameters, which will create a default theme in `$HOME/.local/share/prettygrub/themes/gtc-default`. The "default" template includes a "resources" folder with images, structured by category/usage, that are used as default replacements when the file is missing from the custom template "resources" folder. The custom templates do not use this structure, having all images in the "resources" folder. (It may work even with structured folders, using proper settings, but it is not tested). Using aspect ratio and/or resolution parameters will create another theme (folder) with a similar name `gtc-default-1` and so on. Since it is so easy to create several versions of a theme, after getting the preferred result, you can delete the rest of the similar theme folders and maybe rename the remaining, if you like.
A suggested initial setup is like this:
* Install (instructions on installation at the bottom)
	grub-theme-creator
	horizontal-grub (optional, to have a fake horizontal grub menu)
	grub2-theme-preview (optional, to preview your themes safely)
	imagemagick (to convert images to supplied dimensions, *currently required*)
* Copy the default template folder to your user local path
```
mkdir -p $HOME/.local/share/prettygrub/templates
cp /usr/share/prettygrub/templates/default $HOME/.local/share/prettygrub/templates/
```
* Clone the default template folder for your "custom template", for example `mytheme`. Delete the folders inside new template's "resources" folder.
```
cp $HOME/.local/share/prettygrub/templates/default $HOME/.local/share/prettygrub/templates/mytheme
rm -r $HOME/.local/share/prettygrub/templates/mytheme/resources/*
```
* Copy your custom images for wallpaper, logo, clock (background and tick for countdown timer clock) etc, in custom template "resources" folder.
* Edit `*.conf` files in custom template to comment out (add `#` at line start) default settings. Edit and enable (remove `#`) entries that you want, making sure any referenced files are either in your custom template `resources` folder, or in the default template's resources. It is possible to edit the `theme.txt` file directly, if you know how it works and some feature that is not provided automatically with current settings, like for example to remove the TopMenu (this maybe a feature in the future).
* After editing settings, test your new theme with `grub2-theme-preview`
```
sudo grub2-theme-preview --resolution [RESOLUTION] $HOME/.local/share/prettygrub/templates/mytheme
```

## SCRIPT LOGIC
1. Settings values are acquired in this order:

> [DEFAULT TEMPLATE] is `$HOME/.local/share/prettygrub/templates/default`.
If it is not found, it is `/usr/share/prettygrub/templates/default`

* [DEFAULT TEMPLATE]/templates/default.conf
* [CUSTOM TEMPLATE]/default.conf
* If command-line provides [ASPECT_RATIO] it's used. Else it is read from previous files.
* [DEFAULT TEMPLATE]/templates/[ASPECT_RATIO].conf
* If command-line provides [GRUBxRESOLUTION] it's used. Else it is read from previous files.
* [DEFAULT TEMPLATE]/templates/[GRUBxRESOLUTION].conf
* [CUSTOM TEMPLATE]/default.conf
* [CUSTOM TEMPLATE]/[ASPECT_RATIO].conf
* [CUSTOM TEMPLATE]/[GRUBxRESOLUTION].conf

2. If a setting includes a file, it is first searched in `[CUSTOM TEMPLATE]/resources/`. If it is not found, it is searched in * `[DEFAULT TEMPLATE]/resources/[TYPE]/`, where [TYPE] depends on the specific setting.

3. Distribution icons/images are treated as a group/set, so the used value is the folder name that includes the set. For example `pg-square`. The `prettygrub` set is provided as a sample of how the images are converted, when used in TopMenu (workaround to present distro icons in a "hidden" secondary menu).


## COMMAND_LINE USAGE - HELP
```
grub-theme-creator will create a grub theme based on provided settings,
compatible with horizontal-grub script, to conform a theme with fake-horizontal style.
    Usage:
     -t | --template  [TEMPLATE]
                     The template folder name, containing configuration files and a \"resources\" folder with custom images
                     If the parameter is not used, the default template will be used
     -h | --help
                     This help information message
     -a|--aspectratio [ASPECT_RATIO] {optional}
                     [ASPECT_RATIO] in the form of NUMBER_NUMBER. If the parameter is used, the value is required.
                     This might be helpful in cases when grub resolution is not the same as the real monitor resolution
     -r|--resolution [GRUBxRESOLUTION] {optional}
                     [GRUBxRESOLUTION] in the form of NUMBERxNUMBER. If the parameter is used, the value is required.
                     This might be helpful in cases when grub resolution is not the same as the real monitor resolution
     -p|--prettygrub
                     If you use horizontal-grub type theme, use this parameter to run horizontal-grub when setting as active theme
     -x|--scripted [ACTION]
                     Used to automate theming or horizontal-grub invocation in scripts. Available options are:
                     SaveLocal (saves produced theme to local home folder) SaveSystem (saves produced theme to system folder) [requires admin privileges], SetActiveTheme (as SaveSystem, then copies the theme to /boot/grub/themes, changes /etc/default/grub GRUB_THEME value, runs update-grub and if -p is provided, runs horizontal-grub) [requires admin privileges]
```

## INSTALLATION
* Install grub-theme-creator (suggested, for Archlinux/Manjaro)
```
git clone https://github.com/petsam/grub-theme-creator
cd grub-theme-creator
sudo mkdir -p  /usr/share/prettygrub
sudo cp -r templates  /usr/share/prettygrub/
chmod +x grub-theme-creator
sudo cp grub-theme-creator  /usr/local/bin
```