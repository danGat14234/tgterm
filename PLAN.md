The goal of the application is to kinda handle Coding agents running into terminals, remotely, from the phone.

- We need to implement this telegram bot using botlib: /Users/antirez/hack/botlib. Copy the files you need directly into that repository, they will be part of this repo.
- The goal of the Telegram bot: implementing commands to get the screenshot of one of the applications running in this system, and allow to send such application keystrokes. One can switch different apps using .list and selecting a different app.
- Check what is already implemented in terminal_screenshot.c, you can rename this file and extract the APIs, but it is important that you use what was implemented to take the screenshot and send keystrokes.
- The first user that talks to the bot, gets registered as the owner: we save its user ID on our SQLite database (see botlib usage of SQLlite). Since that moment, messages are only accepted from such Telegram user ID, otherwise discarded.
- By default, if not ran in --danger-mode, only windows matching terminal names are listed: Kitty, Iterm2, Ghostty, Terminal, any other?
- After typing (sending) the user input into the application, after a few seconds we should send the content of the screen.
- Sessions should be listed with .list
- The sessions will be listed with numbers, and the user can connect with .2, .3, ...
- After the session will be connected, we print: "Connected to iTerm ... (window / process title, the same it is now listed with --list)".
- Once a session is connected, the screenshot is sent, as a "connection" welcome, to see what is there.
- Everything typed is sent there.
- If the target window is no longer found, the session is disconencted.
- When a session is disconnected, if we send something, the program shows the .list output.
- Maybe a "refresh" button after screenshots? Clicking could just refresh the image editing the message.
- The bot can be conceived for a single user, no problem with that.
## Emoji Modifier Keys

Use colored heart emojis to represent modifier keys when sending keystrokes.

### Emoji Mappings

| Emoji | Meaning |
|-------|---------|
| ❤️    | Ctrl    |
| 💙    | Alt/Option |
| 💚    | Command |
| 💛    | ESC (standalone, not a modifier) |

### Grammar

1. **1-3 modifier hearts + single character**: Send character with combined modifiers
   - Hearts must be consecutive (no other characters between them)
   - Order doesn't matter: ❤️💙a = 💙❤️a = Ctrl+Alt+A

2. **💛 alone**: Send ESC key
   - Not combinable with other hearts
   - If 💛 appears in a sequence, it sends ESC and resets accumulated modifiers

3. **Regular characters**: Sent as-is

4. **Escape sequences**: `\n`, `\t`, `\\` continue to work

### Examples

| Input | Result |
|-------|--------|
| `❤️c` | Ctrl+C |
| `💚q` | Cmd+Q |
| `❤️💚z` | Ctrl+Cmd+Z |
| `❤️💙💚x` | Ctrl+Alt+Cmd+X |
| `💛` | ESC |
| `ls\n` | l, s, Return |

### UTF-8 Byte Sequences

| Emoji | UTF-8 bytes |
|-------|-------------|
| ❤️    | E2 9D A4 (+ optional EF B8 8F variation selector) |
| 💙    | F0 9F 92 99 |
| 💚    | F0 9F 92 9A |
| 💛    | F0 9F 92 9B |
