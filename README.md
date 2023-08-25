# OAI-Core-gNB-and-Virtual-UE-in-UbuntuServer
### How to install OAI Core, gNB and Virtual UE in ubuntu server 20.04
#### Step 1 — Installing Docker
The Docker installation package available in the official Ubuntu repository may not be the latest version. To ensure we get the latest version, we’ll install Docker from the official Docker repository. To do that, we’ll add a new package source, add the GPG key from Docker to ensure the downloads are valid, and then install the package.

First, update your existing list of packages:

```
sudo apt update
```
Next, install a few prerequisite packages which let apt use packages over HTTPS:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
Then add the GPG key for the official Docker repository to your system:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
Add the Docker repository to APT sources:

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```

This will also update our package database with the Docker packages from the newly added repo.

Make sure you are about to install from the Docker repo instead of the default Ubuntu repo:

```
apt-cache policy docker-ce
```

You’ll see output like this, although the version number for Docker may be different:
"
docker-ce:
  Installed: (none)
  Candidate: 5:19.03.9~3-0~ubuntu-focal
  Version table:
     5:19.03.9~3-0~ubuntu-focal 500
        500 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages 
        "

Finally, install Docker:

```
sudo apt install docker-ce
```
Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it’s running:

```
sudo systemctl status docker
```
#### Step 2 — Installing Docker Compose

Use the following command to download:

```
mkdir -p ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
```

Next, set the correct permissions so that the docker compose command is executable:

```
chmod +x ~/.docker/cli-plugins/docker-compose
```

To verify that the installation was successful, you can run:

```
docker compose version
```
You’ll see output similar to this:

"
Output

Docker Compose version v2.3.3
"

#### Step 3 — Install

```
git clone https://github.com/abhic137/OAI-5G-GNBSIM-SPGWU.git
```
cd OAI-5G-GNBSIM-SPGWU/5gcwithgnbsim

```
sudo docker compose -f docker-compose-basic-nrf.yaml up -d
```

## Build the Gnbsim image OR Pull the image

pulling

```
sudo docker pull rohankharade/gnbsim
sudo docker image tag rohankharade/gnbsim:latest gnbsim:latest
```

Building

```
cd
git clone https://gitlab.eurecom.fr/kharade/gnbsim.git
cd gnbsim
docker build --tag gnbsim:latest --target gnbsim --file docker/Dockerfile.ubuntu.22.04 .
```
### Running the core

```
cd oai-cn5g-fed/docker-compose
sudo docker compose -f docker-compose-basic-nrf.yaml up -d
sudo docker ps -a
sudo docker logs --follow oai-amf
```
### Running the Gnbsim

```
cd oai-cn5g-fed/docker-compose
sudo docker-compose -f docker-compose-gnbsim.yaml up -d gnbsim
sudo docker ps -a
```

Now you can check in the AMF logs that a GNB and a UE are connected to the core.

### Ping test
you can check the ip of ue in the smf logs

```
sudo docker logs oai-smf | grep -o '.*UE IPv4 Address.*' | grep 'UE IPv4 Address'
```

```
docker exec oai-ext-dn ping -c 3 12.1.1.2 #Here we ping UE from external DN container.
docker exec gnbsim ping -c 3 -I 12.1.1.2 google.com #Here we ping external DN from UE (gnbsim) container.
```

### Iperf test

```
sudo docker exec -it oai-ext-dn iperf3 -s   #server
sudo docker exec -it gnbsim iperf3 -c 192.168.70.135 -B 12.1.1.2 #client
```

refrence: https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed/-/blob/master/docs/DEPLOY_SA5G_MINI_WITH_GNBSIM.md



        
