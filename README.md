## Prerequisites

- Install Docker
- Create a new user *without* `sudo` priviliges
- Add the new user to the `docker` group
  - `sudo groupadd docker`
  - `sudo usermod -aG docker <new-user>`
## Usage

Within the new user's home directory, clone the GitHub repository:

    git clone https://github.com/lbushuk/papermc-docker
    
Change environment variables

    nano papermc-docker/Dockerfile

Agree to the EULA, and set the desired version for Minecraft and PaperMC.

Example environment variables:
    
    ENV MC_VERSION="1.20.2" \
          PAPER_BUILD="316" \
          EULA="true" \
          MC_RAM="" \
          JAVA_OPTS=""

Build the docker image with a tag of lbushuk/papermc-docker

    cd papermc-docker/
    docker build -t lbushuk/papermc-docker .

Create a directory for the server files. The newly created user must own this directory.
    
    cd ..
    mkdir server

## Starting the Server

This is the command used to start the server. Please read the following section to understand the options used.

    docker run -p 25565:25565 -v /home/<username>/server:/papermc -d -ti --restart on-failure -e MC_RAM="8G" TZ="America/Winnipeg" --name "minecraft" --user 1001:1001 lbushuk/papermc-docker

## Options
There are several command line options that users may want to specify when utilizing this image. These options are listed below with some brief explanation. An example will be provided with each. In the example, the part that the user can change will be surrounded by angle brackets (`< >`). Remember to *remove the angle brackets* before running the command.
- Port
  - This option must be specified. Use port `25565` if you don't know what this is.
  - Set this to the port number that the server will be accessed from.
  - If RCON is to be used, this option must be specified a second time for port `25575`.
  - `-p <12345>:25565`
  - `-p <12345>:25565 -p <6789>:25575`
- Volume
  - Set this to a name for the server's Docker volume (defaults to randomized gibberish).
  - Alternatively, set this to a path to a folder on your computer.
  - `-v <my_volume_name>:/papermc`
  - `-v </path/to/files>:/papermc`
- Detached
  - Include this to make the container independent from the current command line.
  - `-d`
- Terminal/Console
  - Include these flags if you want access to the server's command line via `docker attach`.
  - These flags can be specified separately or as one option.
  - `-t` and `-i` in any order
  - `-ti` or `-it`
- Restart Policy
  - If you include this, the server will automatically restart if it crashes.
  - Stopping the server from its console will still stop the container.
  - It is highly recommended to only stop the server from its console (not via Docker).
  - `--restart on-failure`
- Name
  - Set this to a name for the container (defaults to a couple of random words).
  - `--name "<my-container-name>"`
- User
  - Sets the user that runs the Docker image. Fixes file permission issues.
  - `--user <uid>:<gid>`
There is one more command line option, but it is a bit special and deserves its own section.
### Environment Variables
Environment variables are options that are specified in the format `-e <NAME>="<VALUE>"` where `<NAME>` is the name of the environment variable and `<VALUE>` is the value that the environment variable is being set to. Please note that setting an evironment variable with no value does not leave it at default; instead, this sets it to an empty string, which may cause issues. This image has four environment variables:
- Minecraft Version
  - **Name:** `MC_VERSION`
  - Set this to the Minecraft version that the server should support.
  - Note: there must be a PaperMC release for the specified version of Minecraft.
  - If this is not set, the latest version supported by PaperMC will be used.
  - Changing this on an existing server will change the version *without wiping the server*.
  - `-e MC_VERSION="1.20.2"`
- PaperMC Build
  - **Name:** `PAPER_BUILD`
  - Set this to the number of the PaperMC build that the server should use (**not the Minecraft version**).
  - If this is not set, the latest PaperMC build for the specified `MC_VERSION` will be used.
  - Changing this on an existing server will change the version *without wiping the server*.
  - `-e PAPER_BUILD="316"`
- EULA
  - **Name:** `EULA`
  - Set this to `true` to accept the Minecraft server EULA
  - **The server will not start if this is not set to `true`**
  - `-e EULA="true"`
- RAM
  - **Name:** `MC_RAM`
  - Set this to the amount of RAM the server can use.
  - Must be formatted as a number followed by `M` for "Megabytes" or `G` for "Gigabytes".
  - If this is not set, Java allocates its own RAM based on total system/container RAM.
  - `-e MC_RAM="<4G>"`
- Java options
  - **Name:** `JAVA_OPTS`
  - **ADVANCED USERS ONLY**
  - Set to any additional Java command line options that you would like to include.
  - By default, this environment variable is set to the empty string.
  - `-e JAVA_OPTS="<-XX:+UseConcMarkSweepGC -XX:+UseParNewGC>"`

## Credit

Modified from https://github.com/Phyremaster/papermc-docker
