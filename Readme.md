1. Start the VMs

If the images have not been built yet:

sudo docker compose build
sudo docker compose up -d

Or do both in one command:

sudo docker compose up -d --build

If you've already built the images previously, simply run:

sudo docker compose up -d
2. Verify they're running
sudo docker ps

You should see something similar to:

ubuntu20-vm   Up (healthy)
ubuntu22-vm   Up (healthy)
ubuntu24-vm   Up (healthy)
3. SSH into the VMs

Ubuntu 20.04

ssh dev@localhost -p 2220

Ubuntu 22.04

ssh dev@localhost -p 2222

Ubuntu 24.04

ssh dev@localhost -p 2224

Password:

dev

To log in as root:

ssh root@localhost -p 2220

Password:

ubuntu

(Change the port for the other VMs.)

4. Check container health
sudo docker ps

Or inspect a specific container:

sudo docker inspect ubuntu20-vm
5. Stop the VMs
sudo docker compose stop
6. Start them again
sudo docker compose start
7. Remove the containers
sudo docker compose down

This removes the containers but keeps the images.

8. Remove everything (containers, images, volumes)
sudo docker compose down --rmi all --volumes
9. Rebuild after changing a Dockerfile
sudo docker compose up -d --build

or

sudo docker compose build --no-cache
sudo docker compose up -d
