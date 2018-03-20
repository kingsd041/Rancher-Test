Following is the list of tests to be executed on a RancherOS release candidate.

### Installing RancherOS

* Using VirtualBox, install to Disk (ros install) and run list of sample commands
> Note: Need to add disk and network card in advance before operation
```
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...
```

* Install using docker-machine

`docker-machine create -d virtualbox --virtualbox-memory "2048" --virtualbox-boot2docker-url rancheros-v130-rc1.iso <machine>`

  * `eval $(docker-machine env <machine>)`
  * Check `docker images`: Pull an image on your local host. SSH into the machine to confirm the image is also in the machine. 
* Install using Amazon AMI for every region(PV/HVM) (`make test` in the `rancher/os-packer` repo will create a vm for each AMI, run all of https://github.com/rancher/os-packer/blob/master/scripts/test.sh and then remove the vm)
 * Test various instance types. (ie. R3, m3, tX, CPU, GPU, etc) Particularly with HVM
> See also aws-run-instace.sh
> Instance type details:
>> t2.medium 2 4
>> r3.large 2 15
>> m3.medium 1 3.75
>> h1.2xlarge 8 32
>> c5.large 2 4
   * Log into RancherOS using SSH from terminal
 * Test passing cloud-config information (debug, ssh key, disabling services)
    * Pass using text 
    * Pass using file
```  
# cat cloud-config.yml
rancher:
  debug: true
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...
```

 * Check version
 `ros -v`
* GCE Image
  * Test different cloud-config (try turning on/off debug)
  * Test SSH keys from Meta-data (Project level)
* Packet
  * Update packet script to test

### Upgrading
* Using old version: 
  * Upgrade/downgrade
>  NOTE: Run container and switch console
> `docker run -itd --name=nginx1 --restart=unless-stopped -p 8000:80 nginx`
* Upgrade in persistent console, check old console
* Upgrade in persistent console with `--upgrade-console`, check new console
* Within Rancher
  * Upgrade/downgrade with existing services

### Consoles
* Test switching into each console

### Engines
* Test passing in cloud config for each docker engine
* Test switching docker engines (known issues from going from Docker 1.12 to Docker 1.10 and Docker 1.11)

### Sample Commands 
(a copy of this is used by `make test` in `rancher/os-packer`)
```
sudo ros -v
sudo ros os list
sudo ros os version
sudo ros service list
sudo ros service enable kernel-headers
sudo system-docker run -d nginx
sudo system-docker ps
sudo ros config export 
docker run -d nginx
docker ps
sudo ros config set rancher.console ubuntu
sudo ros config get rancher.console
sudo ros console list
sudo ros engine list
sudo ros console switch ubuntu
```

### RancherCTL TLS
* Test out generate TLS using end to end example in docs
  * Notes: Easiest is to test with AWS
    * When running `sudo ros tls gen --server -H localhost`, add in `-H <publicDNSfromAWS>` to the command. 
    * To easily copy over the files to your local machine, `sftp rancher@<publicDNSofAWSinstance>:/home/rancher/.docker <<< 'get *'`
    * If you have issues with certs, check that there are no environment variables conflicts.

### ZFS
* Test out ZFS using example in docs

### Testing Rancher
* Confirm Rancher can be installed in RancherOS 
* Set up a RancherOS system to run [Cattle validation tests](http://jenkins-poc.rancher.io:8080/view/validation%20test/job/denise_v2_validation/2/)
* Set up a RancherOS setup to run K8S validation tests

### Services Validation

* Testing Resize with AMIs by launching 10 AMIs with 20 GBs and logging in to make sure it sees all the storage (`df -h`)
* Testing ECS
  * custom ecs version
 
