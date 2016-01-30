Using Docker for Development
============================

Step 1: Install pre-requisites
------------------------------

Mac and Windows only: install Virtualbox

Step 2: Install Docker
----------------------

Mac and Windows: Install Docker Toolbox

Linux: Install Docker and docker-compose

Step 3: Clone TidalLabs/DevOps repo
-----------------------------------

```
mkdir -p ~/Tidal && \
  cd ~/Tidal && \
  git clone https://github.com/TidalLabs/DevOps
```

Step 4: Run Tidal SDK installer
-------------------------------

**Mac and Windows users**: Docker is not reachable from your normal terminal. 
Launch the "Docker Quickstart Terminal" program and use that terminal for the following commands.

**Mac and Linux**:
```
cd ~/Tidal/DevOps/Docker/Swamp
make install
```

**Windows**: (Does not have "make")
```
cd ~/Tidal/DevOps/Docker/Swamp
docker-compose.exe -f docker-compose.dev.yml -f docker-compose.stagrestore.yml up
```

**Note**: It is difficult to tell when this step has finished, because it doesn't exit automatically.

When you see that BOTH "swamp_repos_1" and "swamp_stagrestore_1" have exited, this step has completed. Hit **Ctrl-C** to finish the installation.

Step 5: Run the Tidal Platform
------------------------------

Now that the Tidal SDK is installed, you can start and stop it like so:
