# Example: PHP7 + MySQL + Adminer

## Setup

Add to your ***hosts-file**:
```txt
127.0.0.1  example.local www.example.local db.example.local www.db.example.local
```

Then open a terminal inside of this folder and run:

```bash
docker-compose up
```

Wait for it to start up (until the terminal isnt spammed with log entries anymore)...  

You can now access http://example.local/ or http://www.example.local/ to see a list of fruits and `phpinfo();`.

You can also access http://db.example.local/ or http://www.db.example.local/ to see adminer and manage your database.


## Notes & Explanations

- Files:
    - `.gitignore`: Contains files and folders that the version control system (vcs) "git" should ignore.  
        *For example local configuration overwrites, which may not be suited for the whole team (or potential team. Think ahead)*
    - `.env`: Contains default environment variables for this project
    - `.env.local`: Contains local overwrites for the environment variables (for example your local mysql password)
    - `docker-compose.yml`: Contains instructions on how to compose all project containers and link them together
    - `Dockerfile`: Contains `app` container build instructions (uses latest `php:8` and installs `pdo pdo_mysql` extensions)
    - `dump.sql`: Contains the database dump. It's passed into database container in `docker-compose.yml`
    - `index.php`: Contains the actual website code. It's passed into app container in `docker-compose.yml`
- ***hosts-file**: OS-specifc configuration file to manage custom hostnames.  
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

