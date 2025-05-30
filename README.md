` This repository is heavily based on https://gitlab.com/hpi-potsdam/osm/g5-on-linux/11p-on-linux with small updates to support debian 10 `


The ansible scritp installs all the needed dependencies and patches to enable the usage of 802.11p on the 5.9 Ghz band.

However, after terminating the patch instalation, there are a few steps needed to keep the changes final. It isn't yet automated.

Finally, the scripts refering to the socktap application to be installed as a service

## Running the script

### Client IPs

Firstly edit the file ansible/inventory and add the IP addresses of the devices to be patched

### Client configurations

Edit the file sshd configuration file to allow passwordless access from the root
Install sudo

### Copy the ssh key to the device

`ssh-copy-id -i [key_id] root@[device_address]`

### Run the Script

Before running the script, the connfigurations may be checked by using the following:

`(cd ansible && ansible all --become --ask-become-pass -m ping)`

Then, just run the followign. When over, the ath9k driver will be patched.

`(cd ansible && ansible-playbook --ask-become-pass example-playbook.yml)`

## Finishing

### Installing the neeeded modules

`apt-get install libboost-program-options-dev libgeographic-dev libcrypto++-dev`

### Copying the patched files to the correct dir

`
cp  /root/src-for-11p/linux/drivers/net/wireless/ath/ath.ko /usr/lib/modules/5.10.0-32-amd64/kernel/drivers/net/wireless/ath/`

`cp  /root/src-for-11p/linux/drivers/net/wireless/ath/ath9k/ath9k_hw.ko /usr/lib/modules/5.10.0-32-amd64/kernel/drivers/net/wireless/ath/ath9k/`

`cp  /root/src-for-11p/linux/drivers/net/wireless/ath/ath9k/ath9k_htc.ko /usr/lib/modules/5.10.0-32-amd64/kernel/drivers/net/wireless/ath/ath9k/ `

`cp  /root/src-for-11p/linux/drivers/net/wireless/ath/ath9k/ath9k.ko /usr/lib/modules/5.10.0-32-amd64/kernel/drivers/net/wireless/ath/ath9k/`

`cp  /root/src-for-11p/linux/drivers/net/wireless/ath/ath9k/ath9k_common.ko /usr/lib/modules/5.10.0-32-amd64/kernel/drivers/net/wireless/ath/ath9k/`

### Creating the service files

`/etc/systemd/system/init_v2x.service`
`/usr/local/bin/init-v2x.sh`

### init_v2x.service content

`[Unit]`
`Description=V2X Service Init`

`[Service]`
`ExecStart=/bin/bash /usr/local/bin/init-v2x.sh`
`Restart=on-failure`

`[Install]`
`WantedBy=multi-user.target`

## init-v2x.sh content



`echo "Waiting for services to start"`
`sleep 5s`

`echo "Putting GPS in reading mode"`
`echo "AT+QGPS=1" > /dev/ttyUSB2`
`echo 'AT+QGPSCFG="autogps"' > /dev/ttyUSB2`

`echo "Waiting for GPS"`
`sleep 1s`

`echo "Starting wifi"`
`#start wifi`
`/sbin/iw dev wlp4s0 set type ocb`
`ip link set wlp4s0 up`
`/sbin/iw dev wlp4s0 ocb join 5900 10MHZ`

`echo "Waiting for wifi"`
`#wait for services to init`
`sleep 2s`


`echo "Starting Socktap"
`#init socktap`
`/usr/local/bin/socktap -i wlp4s0 -a ca -p gpsd --station-id 2`

#### sockatp init examples

Initi the service using the CA application with statick gps using the interface wireless wlp4s0. Aditionally, it sends the received data to the server ip 192.168.1.125 and por 9000. The received data is also stored in the file /var/log/v2x/v2x.bin. 

`/usr/local/bin/socktap -a ca -p static -i wlp4s0 --send-to-server --send-to-file --file /var/log/v2x/v2x.bin --print-rx-cam --cam-interval 0 --station-id 4 --server-ip 192.168.1.125 --server-port 9000  &> /var/log/v2x/v2x.log`


## Add the services to boot

`systemctl enable init_v2x.service`
`systemctl start init_v2x.service`




