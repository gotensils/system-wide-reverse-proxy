# Example: PHP7 + MySQL + Adminer + Mailcatcher

## Setup

Add to your ***hosts-file**:
```txt
example.local
www.example.local
db.example.local
www.db.example.local
mails.example.local
www.mails.example.local
```

## Run

Then open a terminal inside of this folder.

```bash
docker-compose up
```

Wait for it to start up (until the terminal isnt spammed with log entries anymore)...  
A fruits table is created and filled in database  
A mail was sent into mailcatcher  
The fruits table and `phpinfo()` is now served under http://example.local/ and http://www.example.local/  
Adminer is now served under http://db.example.local/ and http://www.db.example.local/  
Mailcatcher is now served under http://mails.example.local/ and http://www.mails.example.local/

## Notes

- ***hosts-file**: OS-specifc configuration file for managing custom hostnames.  
    You need admin/root permissions to edit this file.
    - Linux/Mac: `/etc/hosts`  
        1. Open terminal
        1. Type `sudo nano /etc/hosts`
        1. In `nano` editor, add your changes
        1. Save changes using `CTRL+O` (`O`tto, not zero)`
        1. Close `nano` editor using `CTRL+X`
    - Windows: `C:\Windows\system32\drivers\etc\hosts`  
        1. Press Windows-key and type "notepad"
        1. Right-click on Notepad app
        1. Click "Open as administrator"
        1. In Notepads context menu: File > Open file ..
        1. Add your changes
        1. Save changes

