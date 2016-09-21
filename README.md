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

### Basic Installation
#####
### Master Server Side
##### 1. Ubuntu Operation
Update and upgrade operating system
```
sudo apt-get update && sudo apt-get -y upgrade
```  
Add IP Address on host
```
sudo vim /etc/hosts
```  
Set NTP Server
```
sudo apt-get install ntp openssh-server
```
##### 2. JDK 8.0
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
##### 3. Elasticsearch
* ##### Logstash
* ##### Kibana411