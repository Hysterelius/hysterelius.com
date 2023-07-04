+++
title = "Changing emoji font in Kitty"
date = 2023-07-04
updated = 2023-07-04
draft = false

[taxonomies]
categories = ["Features"]
tags = ["twemoji", "kitty"]

[extra]
keywords = "twemoji kitty kitty terminal kitty terminal emoji twitter emoji"
+++

Quick post on how to use twitter emoji in the [Kitty terminal](https://sw.kovidgoyal.net/kitty/). 🖥️✨

<!-- more -->

To begin, ensure that you have the twemoji font installed from the AUR Twemoji package. You can obtain it by following this link: [AUR Twemoji package](https://aur.archlinux.org/packages/ttf-twemoji) For convenient access, the font can be downloaded directly from this link from the AUR page: <https://kojipkgs.fedoraproject.org/packages/twitter-twemoji-fonts/14.0.2/2.fc37/noarch/twitter-twemoji-fonts-14.0.2-2.fc37.noarch.rpm>

Once you have acquired the package, extract the .tff file from the downloaded directory and proceed to install it through your preferred font manager.

Now, to set the new Twemoji font as the default option (following the advice from this [StackExchange answer](https://askubuntu.com/questions/1161516/ubuntu-18-04-gnome-3-28-how-to-change-default-emoji-font-noto-color-emoji)), navigate to the `/etc/fonts/conf.d/60-generic.conf` file.

Locate the following section within the file:
```xml
	<alias binding="same">
		<family>emoji</family>
		<prefer>
			<!-- System fonts -->
			<family>Noto Color Emoji</family> <!-- Google -->
			<family>Apple Color Emoji</family> <!-- Apple -->
			<family>Segoe UI Emoji</family> <!-- Microsoft -->
            <family>Twitter Color Emoji</family> <!-- Twitter -->
			<family>EmojiOne Mozilla</family> <!-- Mozilla -->
			<!-- Third-Party fonts -->
			<family>Emoji Two</family>
			<family>Emoji One</family>
			<!-- Non-color -->
			<family>Noto Emoji</family> <!-- Google -->
			<family>Android Emoji</family> <!-- Google -->
		</prefer>
	</alias>
```

Then edit it to look like this:
```xml
	<alias binding="same">
		<family>emoji</family>
		<prefer>
			<!-- System fonts -->
			<family>Twemoji</family> <!-- Twitter -->
			<family>Noto Color Emoji</family> <!-- Google -->
			<family>Apple Color Emoji</family> <!-- Apple -->
			<family>Segoe UI Emoji</family> <!-- Microsoft -->
			<family>EmojiOne Mozilla</family> <!-- Mozilla -->
			<!-- Third-Party fonts -->
			<family>Emoji Two</family>
			<family>Emoji One</family>
			<!-- Non-color -->
			<family>Noto Emoji</family> <!-- Google -->
			<family>Android Emoji</family> <!-- Google -->
		</prefer>
	</alias>
```
`Twemoji` is the name of the emoji font that was installed.

Then reload the font cache to get the fonts to work: `fc-cache -f -v`.

To check it is working with <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>U</kbd> on the Kitty terminal.

Happy coding! 🧑‍💻

___

### PS:

To get Twemoji to work on all operating systems edit the `/etc/fonts/conf.d/48-generic.conf` to make it look like this:
```xml
<!-- Emoji -->

	<!-- System emoji -->
	<alias binding="same">
		<family>Twemoji</family> <!-- Twitter -->
		<default><family>emoji</family></default>
	</alias>
	<alias binding="same">
		<family>Noto Color Emoji</family> <!-- Google -->
		<default><family>emoji</family></default>
	</alias>
```

Also, check in the `~/.config/fontconfig/fonts.conf` file that this line is in there:
```xml
    <match target="pattern">
        <test qual="any" name="family"><string>Twemoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>FONT</string></edit>
    </match>
```

Reload to confirm changes: `fc-cache -f -v`

---
### PPS:

To get total twemoji across your system you can use <https://github.com/13rac1/twemoji-color-font> to have a more complete experience.