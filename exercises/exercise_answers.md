

### Ch.26
* Lab 26.1
    1. List all currently loaded kernel modules on your system.
    ```
    1. $ lsmod
    ```
    2. Load a currently unloaded module on your system.
    If you are running a distribution kernel, this is easy to find; you can simply look in the /lib/modules/<kernel-version> /kernel/drivers/net directory and grab one. (Distribution kernels come with drivers for every device, filesystem, net- work protocol etc. that a system might need.) However, if you are running a custom kernel you may not have many unloaded modules compiled.
    ```
    2. In the following, substitute whatever module name you used for 8139too. Either of these methods work but, of course,
    the second is easier.
        $ sudo insmod /lib/modules/$(uname -r)/kernel/drivers/net/ethernet/realtek/8139too.ko
        $ sudo /sbin/modprobe 8139too
    ```
    3. Re-list all loaded kernel modules and see if your module was indeed loaded.
    ```
    3. $ lsmod | grep 8139too
    ```
    4. Remove the loaded module from your system.
    ```
    4. Once again, either method works.
        $ sudo rmmod 8139too
        $ sudo modprobe -r 8139too
    ```
    5. Re-list again and see if your module was properly removed.
    ```
    5. $ lsmod | grep 8139too
    ```

### Ch.27
* Lab 27.1
    * **Extra:** udev

### Ch.28
* Lab 28.1
    * **Extra:** KVM
* Lab 28.2
    * **Extra:** virt-manager
* Lab 28.3
    * **Extra:** qemu from command line

### Ch.29
    * Lab 29.1
    1. Make sure Docker is installed. Pick the right command for your distribution from the below:
    ```
        $ sudo yum install docker
        $ sudo zypper install docker
        $ sudo apt install docker.io
    ```
    2. Start the docker service.
    ```
    $ sudo systemctl start docker
    You may want to verify that it is running properly with systemctl status docker:
    ```
    3. Search for the httpd container, with docker search apache
    4. Retrieve the container:
    ```
    $ sudo docker pull docker.io/httpd
    This may take a couple of minutes while all the components download.
    ```
    5. List the installed containers:
    ```
    $ sudo docker images
    ```
    6. List the components associated with the images.
    ```
    $ sudo  docker images --all
    ```
    7. Start the httpd docker container. The terminal will appear to hang as it is now connected to the httpd daemon.
    ```
    c7:/tmp>sudo docker run httpd
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name,
    using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
    ......
    ```
    8. You can open a graphical web browser pointing to the IP address in the above output.
    Or you can use a text-based browser if you are not in a graphical environment, by opening up a new terminal window
    (do not kill the one in which the docker httpd container is running) by doing one of the following commands:
    ```
    $ lynx   http://172.17.0.2
    $ w3m    http://172.17.0.2
    $ elinks http://172.17.0.2
    ```
    using whichever graphical browser is installed on your system.
    9. Stop the container and docker service and clean up.
    ```
    c7:/tmp>sudo docker ps
    c7:/tmp>sudo docker stop b936b0afeb23
    c7:/tmp>sudo docker rmi -f docker.io/httpd
    c7:/tmp>sudo systemctl stop docker
    ```







```

```

