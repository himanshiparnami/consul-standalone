# Consul standalone setup

Consul is an open source service discovery tool which is based and built on Golang. It helps you discovering services application requirements like database, queues, and emails. It comes with some awesome features like Service Discovery, Health Check Status, Key/Value Store, Multi-Datacenter Deployment, and Web UI.

## **Installation steps :**
------------

First upgrade all the available packages and update the system, before installing any new package on the system. 
```
apt update
```

Now install some required packages such as unzip if it is not installed already.
``` 
apt install unzip 
```

Download the latest stable version of Consul from the official Consul Download Webpage using wget command.
```
wget https://releases.hashicorp.com/consul/1.8.4/consul_1.8.4_linux_amd64.zip
```

Now unzip the downloaded package to /usr/local/bin
```
sudo unzip consul_1.8.4_linux_amd64.zip -d /usr/local/bin/
```

After installing Consul, verify that the installation worked by opening a new terminal session and running the command consul
```
consul
```
![img1](https://user-images.githubusercontent.com/101627875/168586970-c6f89442-a850-4c1f-af45-b3a889494e73.png)

### Create Consul Service File

Create a separate user and group for Consul 
```
groupadd --system consul

useradd -s /sbin/nologin --system -g consul consul
```

Create required directories 
```
mkdir /etc/consul.d
mkdir -p /var/lib/consul
```

Change the ownership and permission of those directories
```
chown -R consul:consul /etc/consul.d

chown -R consul:consul /var/lib/consul

chmod -R 775 /var/lib/consul
```

Create a Consul systemd service file with the following command
```
vim /etc/systemd/system/consul.service
```

Add the following lines : 
```
[Unit]
Description=consul service

[Service]
WorkingDirectory=/etc/consul.d
User=consul
Group=consul
ExecStart=/usr/local/bin/consul agent -ui -data-dir /var/lib/consul -config-dir=/etc/consul.d

[Install]
WantedBy=multi-user.target
```
Save and close the file when you are finished.

Now reload the systemd daemon 
```
systemctl daemon-reload 
```

Generate the gossip encryption key. Create the encryption key via the Consul CLI. All agents must have the same encryption key

Generate the encryption key through below command:
```
consul keygen
```

The encryption key will be plain text output:
```
q2GmgNSM3ExP3HVnQX0yKY7HBsaMXwc+iMTDr0vNQkw=
```


Create a Json configuration file for Consul. You can create it with the following command:
```
vim /etc/consul.d/config.json
```
Add below lines 
Provide your server IP
```
{
"bootstrap": true,
"server": true,
"log_level": "DEBUG",
"enable_syslog": true,
"datacenter": "server1",
"addresses" : {
"http": "0.0.0.0"
},
"bind_addr": "your-server-ip",
"node_name": "node-1",
"data_dir": "/var/lib/consul",
"acl_datacenter": "server1",
"acl_default_policy": "allow",
"encrypt": "encryption-key"
}
```

Next, start the Consul service and enable it to start at system reboot with the following command:

```
systemctl start consul
systemctl enable consul
```

Verify the status of the Consul with the following command :
```
systemctl status consul
```

## Access Consul Dashboard

Open your web browser and access the Consul web interface using the URL http://your-server-ip/ui

![img2](https://user-images.githubusercontent.com/101627875/168587227-8673cfa2-878e-4e7a-a063-e82d58a06d99.png)

