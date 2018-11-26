**Deploy ganglia monitoring system on ubuntu 16.04**
##### Cluster Config
```
Master Node: 192.168.2.156
Slave Node: 192.168.2.156 192.168.2.177 192.168.2.46
```

##### Master Deploy
1. Install Lamp stack
```
sudo apt-get install apache2 mariadb-server php7.0 libapache2-mod-php7.0 php7.0-mbstring php7.0-curl php7.0-zip php7.0-gd php7.0-mysql php7.0-curl php7.0-mcrypt
```

2. Install Ganglia
```
sudo apt-get install ganglia-monitor rrdtool gmetad ganglia-webfrontend
```

3. Configure Master
- Apache config
```
sudo cp /etc/ganglia-webfrontend/apache.conf /etc/apache2/sites-enabled/ganglia.conf
```

- Gmetad config
```
# add in /etc/ganglia/gmetad.conf
data_source "my cluster" 50 192.168.2.156:8649
```

- Gmond config
```
# modfiy /etc/ganglia/gmond.conf
cluster {
  name = "my cluster" 
  owner = "unspecified"
  latlong = "unspecified"
  url = "unspecified"
}
udp_send_channel   {
  host = 192.168.2.156   ## Master node IP address
  port = 8649
  ttl = 1
}
udp_recv_channel {
  port = 8649
}
tcp_accept_channel { port = 8649 }
```

- Add gpu metrics
```
sudo cp -r conf.d /etc/ganglia
sudo cp graph.d/*  /usr/share/ganglia-webfrontend/graph.d/
sudo pip install nvidia-ml-py
sudo cp -r python_modules /usr/lib/ganglia
```

- Restart related service
```
sudo systemctl restart ganglia-monitor.service
sudo systemctl status gmetad.service
sudo systemctl restart apache2.service
```

##### Node Deploy
1. Install monitor
```
sudo apt-get install ganglia-monitor
copy /etc/ganglia/gmond.conf from master node
```

2. Add gpu metrics
```
sudo cp -r conf.d /etc/ganglia
sudo cp graph.d/*  /usr/share/ganglia-webfrontend/graph.d/
sudo pip install nvidia-ml-py
sudo cp -r python_modules /usr/lib/ganglia
```

3. Restart related service
```
sudo systemctl restart ganglia-monitor.service
```

##### Dashboard
[http://192.168.2.156/ganglia/]http://192.168.2.156/ganglia/)

##### Reference
[https://github.com/ganglia/gmond_python_modules/tree/master/gpu/nvidia](https://github.com/ganglia/gmond_python_modules/tree/master/gpu/nvidia)
[https://hostpresto.com/community/tutorials/how-to-install-and-configure-ganglia-monitor-on-ubuntu-16-04/](https://hostpresto.com/community/tutorials/how-to-install-and-configure-ganglia-monitor-on-ubuntu-16-04/)
