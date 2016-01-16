Using the Tidal DevBox
======================

This virtual machine comes packaged with Docker, PhpStorm, Firefox, and LastPass -- everything you need to get started with developing on the Tidal Application Platform.

While this virtual machine has all the tools you need, you still need to grab the Tidal SDK -- a managed Docker installation. Open the terminal and navigate to `~/Tidal/DevOps/Docker/Swamp`, and then run `make install`. The first run can take about 10 minutes. 

At this point you can navigate to, eg, lucky.gator.com.

The Tidal code repositories have been downloaded to `/var/www`, but linked to from `~/Tidal` for your convenience. 

Note that `make install` will run in the foreground so that you can view all the logs and make sure the system builds OK. Once both the "swamp-repos" and "stagrestore" containers have finished, the system is ready. You can safely exit the installer with Ctrl-C at this point, and then launch the system like you normally would with `make work`. The "make work" command runs in the background so as not to be intrusive, and the containers can be stopped with "make stopwork".

Installing and Setting Up DevBox
--------------------------------

1. Install VirtualBox and Vagrant
2. Clone the Tidal DevOps repo: `git clone https://github.com/TidalLabs/DevOps`
3. Launch the DevBox image: `cd DevOps/Vagrant/DevBox && vagrant up`
4. Log into the DevBox with user "vagrant" (and same pw)
5. Open a terminal inside DevBox and run `cd ~/Desktop && ./One-Time-Setup.sh` 
6. Run the Tidal SDK installer: `cd ~/Tidal/DevOps/Docker/Swamp && make install`

Quick Docker Reference
----------------------

Any commands involving `docker-compose` or `make` should be run from the directory `~/Tidal/DevOps/Docker/Swamp`. 

First time install / staging data download: `make install` (will write to `mongodata` and `pgdata` directories).

Destroy databases: `make clean` (may require sudo)

Start all containers: `make work`

Stop all containers: `make stopwork`

Show running containers: `docker ps`

Log into a running container: `docker exec -it {name|id} bash`

Log into psql shell: `docker exec -it swamp_swamp-pgsql_1 psql -U tidal -d tidal_component_dev`

Log into mongo shell: `docker exec -it swamp_swamp-mongo_1 mongo {tidal|tidalstats}`

Tail nginx logs: `docker logs -f swamp_swamp-nginx_1`

