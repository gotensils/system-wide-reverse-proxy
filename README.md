# System-wide reverse-proxy

***TL:DR**: We're using `traefik`*

You're probably familiar with `apache` and/or `nginx` and how they're usually used and work:  
Generally speaking, as loadbalancers by routing requests with certain domains, ports, paths, query and even headers to specific resources and services.

But, as convenient as they are, both `apache` and `nginx` have some down-sides in our approach of a closely scoped multi-project multi-environment setup as well as with docker.

- They must listen on port `80` and `443` exclusively.  
    In other words, you can only use one of them as the machines sole entrypoint for incoming web requests.
- You need to add/modify `apache`s/`nginx`s configuration files in order to add/change projects and environments routing.
- After modifying configuration files, you have to reload the running services for them to reapply the configuration files.  
    Thats fine, but sometimes you need to restart the services. => downtimes
- They're not tightly integrated into our setup and for the reasons above, dont really fit our requirements.

So, what we really want, is

- the same ability to route certain requests to specific resources and services,
- while keeping the routing config inside of our projects- or even projects environments scope,
- and to be tightly integrated into our setup, by which I mean docker, since that is used to encapsulate our different resources and (micro)services
- some nice-to-have modern features which would make our everys daily-life easier:
    - automatically generate SSL certificates for each of our projects (or projects environments) seperately  
    For example, using letsencrypt

For that reason I've decided to use `traefik`, as it meets all criterias above in a very satisfying way. :)

## Introduction of Traefik

*(disclaimer: extremely simplified)*

https://traefik.com/

- `traefik` is a loadbalancer similar to `apache` and `nginx` used to route certain requests to specific resources and services  
    (For example: `http://db.example.local` => `http://adminer-container:8080`)
- it requires port `:80` and/or `:443` exclusively
- it lets you keep your routing config inside of your projects:
    you configure your "routes" inside your docker containers (resource or service) configuration using docker `labels`
- does not require a reload/restart after addition/deletion of a project or modifications of configuration
- to be more specific, it does not require configuration **files** at all ;)
- as you can see, it works really well with docker
- it has built-in support for automatically generated SSL certificates using letsencrypt and it only takes a few lines to setup <3  
    Letsencrypt automatically creates valid SSL certificates ("green lock" in browser) for free and keeps them up-to-date, basically forever, as long as `traefik` and your projects containers are running)  
    **Never ever serve an app insecurely under `http://` again! Not even a little example-project!**
- it is written and offers api in `go`, which is also our backend language
    So you could do some really sick stuff because you can directly configure `traefik` from inside your go code using that api.

## Before getting started (!!)

There are 3 mandatory thing required to be done before we should even consider getting started:  

- Disable/stop `apache` or `nginx` (or anything else blocking port `80` or `443`) if it currently runs on your system
- Install [docker](https://www.docker.com/get-started/)
- Install [git](https://git-scm.com/downloads)  
    For windows users, I recommend to install git-bash and context menu integration along with it if asked and then use git-bash terminal during this guide)

## Getting started

We will now setup `traefik` step-by-step as our system-wide reverse-proxy.
*This requires you to stop your running `apache` or `nginx` services.*

We will then setup an example project step-by-step.

We will define for every container to which domains they should bind.  
We can do so by adding docker `labels` to each container config inside of their `Dockerfile` or projects `docker-compose.yml` file.

A container can also be private (for example the database container which should only be accessible internally to our backend service containers)  

In "hosted" environments like *production* or *staging*, we will use and configure the **SSL certificates via letsencrypt** feature.  
In local environments like *development* or *testing*, we will stay with insecure http for now.

### Setup traefik

1. Create a `traefik/` directory somewhere in your development disk space.
1. Open a terminal inside of `traefik/`
1. Download this directory as ZIP and extract somewhere persistent  
    ***TODO**: Provide setup shell-script like: curl https://..setup.sh | bash*
1. Select your environment
    ```bash
    env="development" # or "production"
    cp .env.$env .env # edit .env
    cp docker-compose.$env.yml docker-compose.yml
    ```
1. Edit `.env` and `docker-compose.yml` depending on your needs
1. Make `traefik`s dashboard domains known to our system (See notes for how to)  
    ```txt
    127.0.0.1  traefik.local www.traefik.local dashboard.traefik.local www.dashboard.traefik.local api.dashboard.local
    ```
1. Create external docker network `traefik`
    ```bash
    docker network create --attachable traefik
    ```
1. Start `traefik`
    ```bash
    docker-compose up -d`
    ```

### Setup example projects

Have a look at our [`treafik project examples`](/traefik/examples/README.md).

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

