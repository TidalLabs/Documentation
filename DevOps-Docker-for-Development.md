Using Docker for Development
============================

Step 1: Install pre-requisites
------------------------------

**Mac and Windows only**: install Virtualbox

Step 2: Install Docker
----------------------

**Mac and Windows**: Install Docker Toolbox

**Linux**: Install Docker and docker-compose

Step 3: Clone TidalLabs/DevOps repo
-----------------------------------

```
mkdir -p ~/Tidal && \
  cd ~/Tidal && \
  git clone https://github.com/TidalLabs/DevOps
```

Step 4: Run Tidal SDK installer
-------------------------------

**Mac and Windows Note**: Docker is not reachable from your normal terminal. 
Launch the "Docker Quickstart Terminal" program and use that terminal for the following commands.

**Mac and Linux**:
```
cd ~/Tidal/DevOps/Docker/Swamp
make install
```

**Windows**:

Windows suffers two issues at this step: first, `make` doesn't exist, so the install commands need to be run manually. Second, interactive mode in docker-compose doesn't yet work on Windows, so the commands need to be run in the background, which makes them difficult to monitor (meaning, it's hard to tell when they've finished so that you can move on to the next step).

You may want to open Kitematic at this point so you can better monitor the running containers. A container is stopped when its icon turns into a grey "flatline".


```
cd ~/Tidal/DevOps/Docker/Swamp
docker-compose.exe -f docker-compose.yml -f docker-compose.install.yml run -d repos

# Use docker ps or Kitematic, wait for the swamp_repos_run_* container to stop before continuing...
# Then run:

docker-compose.exe -f docker-compose.yml -f docker-compose.install.yml run -d stagrestore

# Use docker ps or Kitematic, wait for the swamp_stagrestore_run_* container to stop before continuing...
# The mongo and postgres containers WILL continue running, which is OK. 
# As long as stagrestore is finished, you can move on.
```

When this step is complete, you should see new folders in ~/Tidal

Step 5: Reclaim Ownership of Files
----------------------------------

You will likely have to reclaim ownership of the files that Docker created.

**Mac and Linux**: Run `sudo make own`

**Windows**: Run `sudo chown -r [YOUR_USERNAME] ~/Tidal`

Step 6: Start and Stop the Tidal Platform
-----------------------------------------

You can now freely start and stop the Tidal Platform with the following commands. 

Run these commands from `~/Tidal/DevOps/Docker/Swamp`

**To Start:**

    make work

or

    docker-compose up -d

The above commands will run the platform in the background. You can run in the foreground (if you want to watch logs closely) by omitting `-d`.

**To Stop:**

    make stopwork

or

    docker-compose stop


Troubleshooting, Gotchas, Tips
==============================

File Permissions
----------------

Using Docker-Compose
--------------------

Docker-Compose orchestrates building and launching connected containers.
@TODO

Wiping clean and starting over
------------------------------

Sometimes, you just want to wipe everything out and start from scratch. 

To list all containers, including stopped ones, run `docker ps -a`

To remove *all* containers (including non-Tidal ones) and their volumes, run:

    docker ps -aq | xargs docker rm -v

You should also remove everything in the `~/Tidal` directory: `rm -rf ~/Tidal/*`. This, of course, will remove any un-pushed changes in your local codebases.

If you want to delete all images and volumes as well (giving you close to a clean install of Docker), run:

    docker images -aq | xargs docker rmi -f
    docker volume ls -q | xargs docker volume rm 

On Windows and Mac hosts, you can also remove the Docker virtual machine entirely. Close Docker Quickstart Terminal and Kitematic, open VirtualBox, and remove the virtual machine named "default".
