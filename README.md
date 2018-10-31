# kubernetes-rpi-cloud-init
This is intended for development and learning on Kubernetes.  Some security compromises were made in order to facilitate making this process easier, a production ready version would add a lot of security hardening around protecting the cluster admin keys.  This will build a single master Kubernetes cluster with as many worker nodes attached as you care to setup.  The cluster is built using the weave network overlay.  If you want to use flannel or calico there are some very minor changes you can make in the master.yml file to swap those in.

### Pre-requisites
Ensure that you have the flash tool from the hypriot team installed, it can be found here:
https://github.com/hypriot/flash

### Network changes

Edit the master.yml file and set the IP address to a static IP in your network.  Also modify the gateway and the DNS entries to match the systems in your network.

Edit the worker.yml and put the static IP you assigned the master in the last two lines.

### Login changes

Edit the master.yml and the worker.yml replacing the `ssh-authorized-keys` fields to contain your SSH public keys, this lets you SSH in and monitor the build and retrieve any credentials that you need.

### Flash the master

Run the following command to flash the master.  Replacing the --device parameter with the path to the microSD card you want to write the image to.
```bash
$ flash --userdata ./master.yml --device /dev/mmcblk0 https://github.com/hypriot/image-builder-rpi/releases/download/v1.8.1-rc1/hypriotos-rpi-v1.8.1-rc1.img.zip
```

### Flashing the workers

First edit the hostname parameter of the worker.yml file.  Ensure that each time you flash you assign a unique name to it, this is simply for network discovery.  It's possible to continue without this step but it's untested and not recommended.  Run the following command to flash the image replacing the --device with the location of your microSD card:

```bash
flash --userdata ./worker.yml --device /dev/sda https://github.com/hypriot/image-builder-rpi/releases/download/v1.8.1-rc1/hypriotos-rpi-v1.8.1-rc1.img.zip
```
You must run this for each microSD card you create.

### Starting the cluster
Insert the SD cards into the raspberry pis you want to use.  Ensure that they are connected to your network via a physical wire, you can start the nodes in any order as the worker nodes will wait for the master to come up before they continue.


### Security concerns and etc...
The kubectl admin are exposed on the master via nginx, this is remarkabely insecure and should never be used in a production context.

There is a lot of room for improvement, for instance it would be possible to network boot all the worker nodes as well as add a clustered master system.  It's planned in the works.


