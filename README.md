# ELK-Stack, Tunghai University                                                        
#### High Performance Computer Laboratory 2016
# <img src="https://github.com/amranchen/ELK-Stack/blob/master/Images/Elastic%20Logo.png" alt="Build and run unikernels" width="200" height="75">	<img src="https://github.com/amranchen/ELK-Stack/blob/master/Images/Logstash.png" alt="Build and run unikernels" width="200" height="75">	<img src="https://github.com/amranchen/ELK-Stack/blob/master/Images/Kibana4.png" alt="Build and run unikernels" width="200" height="75">

# Elasticsearch, Logstash and Kibana Installation
Elasticsearch is a distributed RESTful search engine built for the cloud.
## Getting Started
---
Please read the following step by step on how to build ELK Stack. If you have any issue or new updating please give us a feedback.
### Requirements
* VMware Workstation with  Ubuntu 14.04.5 LTS (Trusty Tahr)
* Java SE Development Kit 8 (JDK 8)
* Elasticsearch 2.4.0; Logstash 2.4.0; Kibana 4.6.1; Filebeat 1.3.1
* Master Server and Slave Server

### System Architecture
![General Architecture](https://github.com/amranchen/ELK-Stack/blob/master/Images/ELK%20Stack.png)

### Basic Installation 
### Master Server Side
#### 1. Ubuntu Operation
Update and upgrade operating system
```
sudo apt-get update && sudo apt-get -y upgrade
```  
Add IP-Address on host
```
sudo vim /etc/hosts
```  
Set NTP Server
```
sudo apt-get install ntp openssh-server
```
#### 2. JDK 8.0
Add JDK 8.0 repository
```
sudo add-apt-repository -y ppa:webupd8team/java
```
Update operating system
```
sudo apt-get update
```
Install JDK 8.0
```
sudo apt-get -y install oracle-java8-installer
```
Check java version
```
java -version
```
#### 3. Elasticsearch
Download Elasticsearch key package
```
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add
```
Add Elasticsearch repository
```
echo "deb http://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list
```
Install Elasticsearch
```
sudo apt-get -y install elasticsearch
```
Edit elasticsearch.yml file
```
sudo vim /etc/elasticsearch/elasticsearch.yml
with:
network.host	: [IP-Address]
http.port		: 9200
```
Restart elasticsearch service
```
sudo service elasticsearch restart
```
Set elasticsearch as default service
```
sudo update-rc.d elasticsearch defaults 95 10
```
Elasticsearch testing
```
curl -X GET 'http://IP-Address:9200'
```
#### 4. Kibana
Download Kibana package
```
wget https://download.elastic.co/kibana/kibana/kibana-4.6.1-linux-x86_64.tar.gz
```
Extract file
```
tar xvf kibana-*.tar.gz
```
Edit kibana.yml file
```
sudo vim ~/kibana-4*/config/kibana.yml
with:
server.port		: 5601
server.host		: "[IP-Address]"
elasticsearch.url	: http://[IP-Address]:9200
```
Create directory
```
sudo mkdir -p /opt/kibana
```
Copy kibana path to /opt/kibana
```
sudo cp -R ~/kibana-4*/* /opt/kibana/
```
Give permission for /opt/kibana
```
sudo chown -R [username]: /opt/kibana
```
Download service package
```
cd /etc/init.d && sudo wget https://gist.githubusercontent.com/thisismitch/8b15ac909aed214ad04a/raw/bce61d85643c2dcdfbc2728c55a41dab444dca20/kibana4
```
Give permission for /etc/init.d/kibana4 path
```
sudo chmod +x /etc/init.d/kibana4
```
Give permission for kibana path
```
sudo chmod +x /etc/init.d/kibana4
```
Start Kibana service
```
sudo service kibana4 start
```
Check kibana version
```
bin/kibana version
```
#### 5. Nginx

```
sudo apt-get install nginx -y apache2-utils
```

```
sudo htpasswd -c /etc/nginx/htpasswd.users kibanaadmin
with password: xxxxxx
```

```
sudo vim /etc/nginx/sites-available/default
with add this following script:
server {
    listen 80;

    server_name <your ip here>;

    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/htpasswd.users;

    location / {
        proxy_pass http://<your ip here>:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;        
    }
}
```
Restart nginx service
```
sudo service nginx restart
```
Check this following web:
http://IP-Address

#### 6. Logstash
Download Logstash package
```
echo 'deb http://packages.elasticsearch.org/logstash/2.4/debian stable main' | sudo tee /etc/apt/sources.list.d/logstash.list
```
Update operating system
```
sudo apt-get update
```
Install Logstash
```
sudo apt-get install logstash
```
Start Logstash service
```
sudo service logstash start
```
Check Logstash version
```
bin/logstash --version
```
#### 7. Generate SSL
Create directory
```
sudo mkdir -p /etc/certs
```
Edit openssl configuration
```
sudo vim /etc/ssl/openssl.cnf
with:
subjectAltName = IP: [Logstash IP-Address]
```
Entrance following path
```
cd /etc/certs
```
Generate SSL certificate
```
sudo openssl req -config /etc/ssl/openssl.cnf -x509 -days 3650 -batch -nodes -newkey rsa:2048 -keyout server.key -out server.crt
```
Configure central.conf
```
sudo vim /etc/logstash/conf.d/central.conf
with:
input {
 beats {
			port => 5044
			ssl => true
			ssl_certificate => “/etc/certs/server.crt”
			ssl_key => “/etc/certs/server.key”
} 
   }
	filter{}
	output{
		elasticsearch {
				hosts => “<IP Address>”
				sniffing => true
				manage_template => false
				index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
document_type => "%{[@metadata][type]}"
}
}
```
Compile central config file
```
sudo service logstash configtest
```
Restart logstash service
```
sudo service logstash restart
```
#### 8. Load Kibana Dashboard
Download beat dashboard
```
curl -L -O http://download.elastic.co/beats/dashboards/beats-dashboards-1.3.1.zip
```
Install zip
```
sudo apt-get -y install unzip
```
Extract beats dashboards
```
unzip beats-dashboards-*.zip
```
Entrance beats-dashboards path
```
cd beats-dashboards-*
```
Edit load.sh file
```
sudo vim load.sh
with: 
ELASTICSEARCH=http://[IP-Address]:9200
```
Compile load.sh file
```
./load.sh
```
#### 9. Load Filebeat Index Template in Elasticsearch
Download Filebeat index template
```
https://gist.githubusercontent.com/thisismitch/3429023e8438cc25b86c/raw/d8c479e2a1adcea8b1fe86570e42abab0f10f364/filebeat-index-template.json
curl -XPUT 'http://IP-Address:9200/_template/filebeat?pretty' -d@filebeat-index-template.json
```
Restart Kibana service
```
sudo service kibana4 restart
```
#### 10. SSL Copying
Copy Master's SSL to Slave
```
scp /etc/certs/server.crt user@client_server_private_address:/tmp
scp /etc/certs/server.key user@client_server_private_address:/tmp
```
Move certificate and key
```
sudo mv server.crt server.key /etc
```

### Slave Server Side
#### 1. Install Filebeat
Build beats source-list
```
echo "deb https://packages.elastic.co/beats/apt stable main" |  sudo tee -a /etc/apt/sources.list.d/beats.list
```
Download GPG Key of Elasticsearch
```
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
Update operating system
```
sudo apt-get update
```
Install Filebeat
```
sudo apt-get install filebeat
```
### 2. Configure Filebeat
Edit filebeat.yml file
```
sudo vim /etc/filebeat/filebeat.yml
Edit for:
* paths:
    - /var/log/*.log
* document_type: syslog
Comment for
output:
  ### Elasticsearch as output
  # elasticsearch
  ...
  ...

  # hosts: ["localhost:9200"]

Uncomment for:
logstash:
host: ["IP-Address:5044"]

Add for:
bulk_max_size: 1024

Uncomment for:
tls:
certificate_authorities: ["/etc/server.crt"]
```
Restart filebeat service
```
sudo service filebeat restart
```
Default setting for service
```
sudo update-rc.d filebeat defaults 95 10
```
Visit Kibana web:
http://IP-Address:5601
![Kibana Result](https://github.com/amranchen/ELK-Stack/blob/master/Images/Kibana%20Web.png)
