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

**Windows**: (Does not have "make", so you must run the commands manually)
```
cd ~/Tidal/DevOps/Docker/Swamp
docker-compose.exe -f docker-compose.yml -f docker-compose.install.yml run repos
docker-compose.exe -f docker-compose.yml -f docker-compose.install.yml run stagrestore
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

**To Stop:**

    make stopwork

or

    docker-compose stop
