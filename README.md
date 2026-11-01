<p align="center">
  <img src="docs/icon.png" width="96" alt="Emojify icon">
</p>

<h1 align="center">Emojify</h1>

<p align="center">
  An emoji picker for KDE Plasma that types the emoji right where you were writing.
</p>

<p align="center">
  <a href="https://github.com/Amariless/Emojify-releases/releases/download/v0.3.0/emojify-0.3.0-linux.tar.gz"><img src="https://img.shields.io/badge/KDE_Plasma_6-Download_.tar.gz-17161c?style=for-the-badge&labelColor=3b9eda&logo=kde&logoColor=white" alt="Download for KDE Plasma 6"></a>
</p>

<p align="center">
  <img src="docs/screenshot.png" width="620" alt="Emojify showing heart emojis over the desktop">
</p>

## How it works

1. Press <kbd>Meta</kbd>+<kbd>.</kbd>
2. Type what you're looking for.
3. Press <kbd>Enter</kbd>.

The emoji appears in the window you were using: WhatsApp Web, Discord, Telegram, your browser or the terminal. No copying and pasting by hand.

<table>
  <tr>
    <td width="33%" valign="top">
      <h3>Search your way</h3>
      <p>Understands English and Spanish at the same time, ignores accents and capitals, and forgives typos.</p>
    </td>
    <td width="33%" valign="top">
      <h3>Keyboard first</h3>
      <p>It opens instantly with your recent emojis. Arrows to move, <kbd>Enter</kbd> to insert, <kbd>Esc</kbd> to close.</p>
    </td>
    <td width="33%" valign="top">
      <h3>Out of your way</h3>
      <p>Works offline and puts back whatever you had copied after it inserts the emoji.</p>
    </td>
  </tr>
</table>

**Also:** skin tones, flags, favorites, inserting several emojis in a row without closing, and an interface in English, Spanish, Portuguese, French, German and Italian.

## Install

You need KDE Plasma 6. It's tested on CachyOS and should work on any distribution with Plasma 6, like Fedora KDE, openSUSE, Kubuntu or KDE neon.

1. [Download the .tar.gz](https://github.com/Amariless/Emojify-releases/releases/download/v0.3.0/emojify-0.3.0-linux.tar.gz).
2. Open a terminal where you downloaded it and run:

```bash
tar xzf emojify-*-linux.tar.gz
cd emojify-*-linux
./install.sh
```

3. Press <kbd>Meta</kbd>+<kbd>.</kbd> and try it.

The installer takes care of everything and only asks for your password if it needs to install something on your system. To uninstall, run `./uninstall.sh` from the same folder.

## Feedback

Found a bug, have an idea or just want to say something? Each button opens a short form.

<p>
  <a href="https://github.com/Amariless/Emojify-releases/issues/new?template=bug.yml"><img src="https://img.shields.io/badge/Report_a_bug-3b9eda?style=for-the-badge" alt="Report a bug"></a>
  <a href="https://github.com/Amariless/Emojify-releases/issues/new?template=idea.yml"><img src="https://img.shields.io/badge/Suggest_an_idea-3b9eda?style=for-the-badge" alt="Suggest an idea"></a>
  <a href="https://github.com/Amariless/Emojify-releases/issues/new?template=comment.yml"><img src="https://img.shields.io/badge/Leave_a_comment-3b9eda?style=for-the-badge" alt="Leave a comment"></a>
</p>

<details>
<summary><b>Keyboard shortcuts</b></summary>
<br>

| Key | Action |
|---|---|
| <kbd>Meta</kbd>+<kbd>.</kbd> | Open or close (you can change it) |
| Typing | Search, no need to click first |
| Arrows | Move between emojis |
| <kbd>Enter</kbd> | Insert |
| <kbd>Shift</kbd>+<kbd>Enter</kbd> | Insert without closing (or the other way around, in settings) |
| <kbd>Space</kbd> with an empty search | Choose the skin tone of that emoji |
| <kbd>Ctrl</kbd>+<kbd>D</kbd> | Add to or remove from favorites |
| <kbd>Tab</kbd> | Switch between recents, favorites and categories |
| <kbd>Ctrl</kbd>+<kbd>,</kbd> | Settings |
| <kbd>Esc</kbd> | Clear the search or close |

</details>

<details>
<summary><b>If something doesn't work</b></summary>
<br>

**The shortcut does nothing.** Run `emojify --setup` and check in *System Settings → Shortcuts → KWin* that "Emojify" has <kbd>Meta</kbd>+<kbd>.</kbd> and nothing else uses it.

**It opens, but the emoji isn't pasted and you get a notification.** The service that sends the paste isn't running. Check `systemctl --user status ydotool.service` or run `./install.sh` again. Meanwhile, the emoji is copied and you can paste it with <kbd>Ctrl</kbd>+<kbd>V</kbd>.

**In one specific app it pastes late, or pastes what you had copied before.** Raise `paste_delay_ms` in the settings (to 80, for example). If it's a terminal Emojify doesn't recognize, add its window class to `terminals`.

**Deleting a flag in the Chrome or Brave address bar leaves a stray letter.** That's not Emojify: a flag is made of two special letters and Chromium's address bar deletes them one at a time. In regular text boxes it deletes the whole flag.

**Why only KDE Plasma 6?** Emojify relies on KWin, Plasma's window manager, to put the emoji back in your window. It doesn't work on Plasma 5, GNOME or Hyprland, and Windows already has its own picker with <kbd>Win</kbd>+<kbd>.</kbd>.

**Logs:** `journalctl --user -u emojify -f`

</details>

<details>
<summary><b>How it inserts the emoji</b></summary>
<br>

On Plasma Wayland, no app is allowed to type straight into another window. Emojify uses the clipboard without you noticing: it saves what you had copied, copies the emoji (without adding it to Klipper's history), gives the focus back to your window, sends <kbd>Ctrl</kbd>+<kbd>V</kbd> (or <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>V</kbd> in terminals) and restores what you had. It all takes a few milliseconds. If something fails, the emoji stays copied and you get a notification: it never fails silently.

To do this it installs a small KWin script and a window rule, which you can see in System Settings. `emojify --unsetup` removes them and gives <kbd>Meta</kbd>+<kbd>.</kbd> back to Plasma's own picker. You can update the emoji list with `emojify --update`. Requires Python 3.11 or newer.

</details>

## Credits

Icon by [Sofyan Rofiq](https://www.flaticon.es/autores/sofyan-rofiq) on Flaticon. Emoji data from [Emojibase](https://emojibase.dev) and [Unicode CLDR](https://cldr.unicode.org). Emojis from Google's Noto Color Emoji.

Free for personal use. The full terms are in [LICENSE](LICENSE).
